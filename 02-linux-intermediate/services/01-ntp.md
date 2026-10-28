# Chrony on RHEL 10: setup and how it works

RHEL 10 uses chrony for Network Time Protocol (NTP). The package provides two programs:

- `chronyd` is the background service. It synchronizes the system clock and can serve time to other machines.
- `chronyc` is the command-line tool for checking and controlling `chronyd`.

Chrony is installed by default on RHEL 10, but you can install it explicitly if needed. Red Hat Documentation

## 1. The basic idea

A machine gets time measurements from one or more NTP sources. Chrony estimates the offset between its clock and those sources, then adjusts the system clock gradually, called slewing. This avoids sudden time jumps that can disrupt applications. For a large initial error, chrony can be configured to step the clock instead.

NTP uses a hierarchy called stratum. A stratum 1 source is directly connected to a reference clock; machines that synchronize from it have a higher stratum number. Lower stratum is generally closer to the reference clock, but stratum alone does not prove a source is accurate.

`iburst` asks a source for several quick measurements when first contacted, which helps the initial synchronization happen sooner.

## 2. Package, service, and files

Install or check the package:

```bash
sudo dnf install -y chrony
rpm -q chrony
```

Check and start the service:

```bash
systemctl status chronyd
sudo systemctl enable --now chronyd
systemctl is-active chronyd
systemctl is-enabled chronyd
```

The main configuration file is `/etc/chrony.conf`. Other useful paths include:

| Path | Purpose |
| --- | --- |
| `/usr/sbin/chronyd` | Chrony daemon |
| `/usr/bin/chronyc` | Monitoring and control command |
| `/var/lib/chrony/drift` | Stored estimate of the system clock’s rate of drift |
| `/etc/chrony.keys` | Optional keys for authenticated NTP configurations |
| `/var/run/chrony/chronyd.sock` | Local control socket used by `chronyc` |

RHEL’s documented default configuration includes the drift file and other chrony settings. Inspect your existing file before changing it; keep valid defaults and change the time sources you need. Red Hat Documentation

```bash
sudo less /etc/chrony.conf
sudo cp -a /etc/chrony.conf /etc/chrony.conf.bak
sudo vi /etc/chrony.conf
```

## 3. Configure a client

A client synchronizes its own clock from time sources. In `/etc/chrony.conf`, add your organization’s approved source, or update the existing source lines:

```text
server time.example.net iburst
```

For a pool of servers, use a `pool` directive instead:

```text
pool pool.example.net iburst
```

Replace these example names with sources provided by your organization or environment. Multiple sources provide alternatives if one is unavailable. After editing the file:

```bash
sudo systemctl restart chronyd
systemctl status chronyd
```

A common setting in the file is:

```text
makestep 1.0 3
```

This allows chrony to step the clock if the offset exceeds one second during the first three clock updates after startup. It can help correct a badly wrong clock at boot. Do not use `chronyc makestep` casually on a live system: a sudden clock jump can affect running applications.

## 4. Configure a host to serve NTP to a LAN

An NTP server should normally synchronize upstream first, then provide that time to its clients. Keep its upstream server or pool sources in `/etc/chrony.conf`, and add an `allow` line for the client subnet:

```text
server upstream.example.net iburst
allow 192.168.1.0/24
```

Replace the example subnet with the actual client network in CIDR notation. The `allow` directive lets clients from that network request NTP service. Avoid `allow all` on a network-connected host.

Enable the NTP service in the firewall. First check which zone is active:

```bash
sudo firewall-cmd --get-active-zones
```

Then add the NTP service to the relevant zone. For example, if the zone is `public`:

```bash
sudo firewall-cmd --permanent --zone=public --add-service=ntp
sudo firewall-cmd --reload
sudo firewall-cmd --zone=public --query-service=ntp
sudo systemctl restart chronyd
```

If your active zone is different, substitute that zone for `public`. Red Hat documents `allow` for permitting NTP clients; the firewall must also permit their NTP traffic. Red Hat Documentation

Do not add `local stratum` for a normal network server. That setting is for special isolated-network cases where there is no upstream time source; it makes the host advertise itself as a time source without verifying its clock against an external reference. Red Hat Documentation

## 5. Ports and firewall behavior

| Traffic | Port | When it is needed |
| --- | --- | --- |
| NTP requests and replies | UDP 123 | Client talks to its source; a server must allow incoming client requests |
| Remote `chronyc` monitoring | UDP 323 | Only if you deliberately enable remote chrony monitoring |
| Local `chronyc` control | Unix socket | Used locally by default; does not require opening a network port |

A client generally needs outbound UDP 123 and the corresponding replies. It does not need an inbound NTP firewall rule. An NTP server needs inbound UDP 123 permitted from the client networks it serves. Chrony’s remote monitoring uses a separate command port; Red Hat documents UDP 323 and requires explicit configuration for remote access. Keep that access restricted and disabled unless you need it. Red Hat Documentation

## 6. Verify synchronization

Run these on either a client or a server:

```bash
chronyc tracking
chronyc sources -v
chronyc sourcestats -v
timedatectl
```

Useful checks:

- In `chronyc sources -v`, `^*` marks the selected NTP source. `^+` marks another usable source. `^?` means the source is not currently selectable, often because it has not replied successfully yet.
- In `chronyc tracking`, check Reference ID, Stratum, System time, and Leap status. Leap status: Normal is a good sign.
- In `timedatectl`, look for `System clock synchronized: yes`.
- On a server, `chronyc clients` lists clients that have accessed it, if client access is configured. Red Hat Documentation

For a server, you can also check whether `chronyd` is listening for NTP traffic:

```bash
sudo ss -ulpn | grep ':123'
```

A client-only configuration may not listen for incoming NTP requests.

## 7. Useful operational commands

```bash
# Restart after changing /etc/chrony.conf
sudo systemctl restart chronyd

# Read the service log for this boot
sudo journalctl -u chronyd -b

# Ask chronyd to step the clock immediately
sudo chronyc makestep

# List configured/known time sources
chronyc sources -v

# Show synchronization and clock-drift status
chronyc tracking

# For a server: list clients
sudo chronyc clients
```

Use `chronyc makestep` only when you specifically need an immediate correction and have considered the effect of changing system time. Red Hat documents it as the command to step the clock immediately. Red Hat Documentation

## 8. Quick troubleshooting sequence

If the host is not synchronizing, check service, config, and connectivity in that order:

```bash
systemctl status chronyd
sudo journalctl -u chronyd -b
grep -E '^(server|pool|allow|makestep|sourcedir)' /etc/chrony.conf
chronyc activity
chronyc sources -v
chronyc tracking
timedatectl
```

For a LAN client that cannot reach your NTP server, verify that:

1. The server has an upstream time source and is synchronized.
2. The server’s `/etc/chrony.conf` allows the client’s subnet.
3. UDP 123 is permitted by the server firewall and any network firewall between the hosts.
4. The client points to the server’s reachable hostname or IP address.

The key distinction: `allow` controls which machines may use the host as an NTP server; it is not needed on a client-only machine.
