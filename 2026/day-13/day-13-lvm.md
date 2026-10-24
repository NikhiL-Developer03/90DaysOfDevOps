# Day 13 – Linux Storage Basics, LVM Revision, IP Address & MAC Address

## Objective

Day 13's assigned topic was Linux Volume Management (LVM). I completed and practiced some storage-related commands and also learned about IP addresses and MAC addresses through practical work.

---

## 1. Check Current Storage

### Check disk usage

```bash
df -h
```

**Purpose:** Displays filesystem space usage in human-readable units, including total, used, available space, and mount points.

### List block devices

```bash
lsblk
```

**Purpose:** Shows disks, partitions, and logical volumes in a tree-like view. It can also show where a device is mounted.

### Other LVM inspection commands

```bash
pvs
vgs
lvs
```

- `pvs` lists LVM physical volumes (PVs).
- `vgs` lists LVM volume groups (VGs).
- `lvs` lists LVM logical volumes (LVs).

Run these commands to inspect your system; the output depends on which disks and LVM volumes exist.

---

## 2. Understand the LVM Storage Layers

LVM lets Linux storage be managed in flexible layers:

```text
Physical disk or partition
          |
          v
Physical Volume (PV)
          |
          v
Volume Group (VG)
          |
          v
Logical Volume (LV)
          |
          v
Filesystem (for example, ext4)
          |
          v
Mount point (directory)
```

- **PV (Physical Volume):** A disk or partition prepared for LVM.
- **VG (Volume Group):** A pool of storage made from one or more PVs.
- **LV (Logical Volume):** A virtual block device allocated from a VG.
- **Filesystem:** Organizes files on the logical volume, for example `ext4`.
- **Mount point:** A directory through which the mounted filesystem is accessed.

The challenge describes creating a PV, VG, and LV. Only include those as completed tasks if you actually ran and verified those commands.

---

## 3. Format and Mount a Logical Volume

The reference notes use an existing LVM setup named `tws-vg` and `tws-lv`.

### Inspect the existing volume

```bash
lsblk
pvs
vgs
lvs
```

### Format as ext4 — destructive operation

```bash
sudo mkfs.ext4 /dev/tws-vg/tws-lv
```

**Purpose:** Creates an ext4 filesystem on the logical volume. **Warning:** Formatting destroys existing filesystem data on the target device. Only run this when you have confirmed the exact device and are certain it can be formatted. Do not rerun just to reproduce the notes.

### Create a mount point and mount the volume

```bash
sudo mkdir -p /mnt/tws-space
sudo mount /dev/tws-vg/tws-lv /mnt/tws-space
```

### Verify the mount

```bash
df -h /mnt/tws-space
lsblk
```

**Purpose:** `mkdir -p` creates the directory if needed; `mount` attaches the filesystem at that directory; `df -h` and `lsblk` help verify the result.

These commands are from the reference notes. Keep them in your personal record only if they match what you actually ran.

---

## 4. Extend an LVM Logical Volume

The reference notes show a logical-volume extension example:

```bash
sudo lvextend -L +2G /dev/tws-vg/tws-lv
```

This requests an additional 2 GiB for the logical volume, provided the volume group has enough free space. Extending the LV alone does not necessarily enlarge the filesystem.

### Extend the logical volume and resize its filesystem together

```bash
sudo lvextend -L +2G -r /dev/tws-vg/tws-lv
```

The `-r` option asks LVM to resize the filesystem as part of the operation. Check the result with:

```bash
sudo lvs
df -h /mnt/tws-space
lsblk
```

**Important:** Do not run both extension examples one after another unless you intentionally want to add storage twice and the VG has enough free space. Confirm the current LV size and free VG space first.

---

## 5. Separate Block Device Example from the Reference Notes

The reference also shows a separate device, `/dev/nvme3n1`, mounted at `/mnt/tws-space2`.

```bash
lsblk
sudo mkdir -p /mnt/tws-space2
```

Formatting a device is destructive:

```bash
sudo mkfs.ext4 /dev/nvme3n1
```

Mount and verify:

```bash
sudo mount /dev/nvme3n1 /mnt/tws-space2
df -h /mnt/tws-space2
lsblk
```

**Safety warning:** `/dev/nvme3n1` is only an example from the reference notes and may not exist on your machine. Never format a disk based only on its name. Confirm the device using `lsblk` and ensure it contains no needed data. Do not run the format command unless you deliberately intend to erase that device.

---

## 6. IP Address and MAC Address – Practical Learning

Alongside the assigned storage topic, I also practiced learning about IP addresses and MAC addresses.

### Useful commands

```bash
ip addr
ip link
ip route
```

- `ip addr` displays network interfaces and their assigned IP addresses.
- `ip link` displays network interfaces and their link-layer details, including MAC addresses.
- `ip route` displays routing information, including the default route when configured.

For a shorter view, you can also use:

```bash
ip -br addr
ip -br link
```

### What is an IP address?

An IP address is a logical network address used to identify an interface for communication over an IP network. An interface may have a private IP address for communication within a local network and may reach the public internet through a router or other gateway.

### What is a MAC address?

A MAC address is a link-layer address associated with a network interface. It is used for communication on the local network segment. It is not the same thing as a public IP address, and a MAC address is generally not used by internet routers to forward packets across the entire internet.

### Key difference

| IP address | MAC address |
|---|---|
| Used at the IP/network layer | Used at the link layer on a local network |
| Helps route IP traffic between networks | Helps deliver frames on the local link |
| Can change when network configuration changes | Usually tied to the interface, but can be changed or randomized by software |
| Example format: `192.168.1.20` (IPv4) | Example format: `02:1A:2B:3C:4D:5E` |

Your actual IP and MAC values depend on the interface and network. Capture your own command output rather than copying example values.

---

## 7. Commands Reference

```bash
# Storage and filesystem usage
df -h
lsblk

# LVM inspection
sudo pvs
sudo vgs
sudo lvs

# Network interfaces, IP addresses, MAC addresses, and routes
ip addr
ip link
ip route
ip -br addr
ip -br link
```

The following commands are included as examples from the reference notes, not a claim that all were completed:

```bash
# Existing logical volume examples — verify your own device names first
sudo mkdir -p /mnt/tws-space
sudo mount /dev/tws-vg/tws-lv /mnt/tws-space
sudo lvextend -L +2G -r /dev/tws-vg/tws-lv
df -h /mnt/tws-space
```

---

## 8. What I Learned

1. **Linux storage inspection:** `lsblk` and `df -h` help me understand the available block devices, mounted filesystems, and storage usage.
2. **LVM structure:** LVM organizes storage through Physical Volumes, Volume Groups, and Logical Volumes. Extending an LV and resizing its filesystem are related but distinct steps; `lvextend -r` can handle both together.
3. **Networking basics:** I practiced using `ip addr`, `ip link`, and `ip route` to inspect network interfaces, IP addresses, MAC addresses, and routes.

---

## 9. Verification and Screenshots Checklist

Before committing this file, capture screenshots of commands you actually ran and their real outputs.

- [ ] `lsblk`
- [ ] `df -h`
- [ ] `pvs`, `vgs`, and `lvs` (if available on your system)
- [ ] The mount/extension verification commands you actually completed
- [ ] `ip addr` or `ip -br addr`
- [ ] `ip link` or `ip -br link`
- [ ] `ip route`

Do not mark a task as completed unless you performed it and verified the output.

---

