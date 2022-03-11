# Navy Lead Fox Terrier

## HowTo Build

You can build this image and do your own tests with https://github.com/systemd/mkosi

Just run `mkosi` in the `root`-directory of this repository.

The image itself is Arch Linux based, so you need `pacman` and `pacstrap`. But you should be able to use any other distribution, as this setup is only configuring systemd. See `mkosi --help` for more information.

## Purpose

Setting up some testing for a colleague of mine, who got this problem at $customer. I try to mimic the hardware as used by the customer to make this actually work, but in the end $colleague has to tell me if this works.

The original setup was done with [Netplan.io](https://netplan.io) with the `networkd` renderer, but I want to reduce the error surface by using `sd-networkd` directly.

```mermaid
graph TD
    ens1f0["<p>ens1f0</p>
        <hr/>
        <table>
          <tr><th>Type</th><td>Ethernet Controller</td></tr>
          <tr><th>MTU</th><td>1500</td></tr>
          <tr><th>Driver</th><td>mlx5_core</td></tr>
        </table>
    "]
    ens1f1["<p>ens1f1</p>
        <hr/>
        <table>
          <tr><th>Type</th><td>Ethernet Controller</td></tr>
          <tr><th>MTU</th><td>1500</td></tr>
          <tr><th>Driver</th><td>mlx5_core</td></tr>
        </table>
    "]
    ens4f0["<p>ens4f0</p>
        <hr/>
        <table>
          <tr><th>Type</th><td>Ethernet Controller</td></tr>
          <tr><th>MTU</th><td>1500</td></tr>
          <tr><th>Driver</th><td>mlx5_core</td></tr>
        </table>
    "]
    ens4f1["<p>ens4f1</p>
        <hr/>
        <table>
          <tr><th>Type</th><td>Ethernet Controller</td></tr>
          <tr><th>MTU</th><td>1500</td></tr>
          <tr><th>Driver</th><td>mlx5_core</td></tr>
        </table>
    "]
    ens10f0["<p>ens10f0</p>
        <hr/>
        <table>
          <tr><th>Type</th><td>Ethernet Controller</td></tr>
          <tr><th>MTU</th><td>9000</td></tr>
          <tr><th>Driver</th><td>qede</td></tr>
        </table>
    "]
    ens10f1["<p>ens10f1</p>
        <hr/>
        <table>
          <tr><th>Type</th><td>Ethernet Controller</td></tr>
          <tr><th>MTU</th><td>9000</td></tr>
          <tr><th>Driver</th><td>qede</td></tr>
        </table>
    "]
    bond_xfer["<p>bond_xfer</p>
        <hr/>
        <table>
          <tr><th>Type</th><td>bond</td></tr>
          <tr><th>MTU</th><td>1500</td></tr>
          <tr><th>Options</th><td>
            <table>
              <tr><th>lacp-rate</th><td>fast</td></tr>
              <tr><th>mode</th><td>802.3ad</td></tr>
              <tr><th>transmit-hash-policy</th><td>layer3+4</td></tr>
            </table>
          </td></tr>
        </table>
    "]
    bond_mgmt["<p>bond_mgmt</p>
        <hr/>
        <table>
          <tr><th>Type</th><td>bond</td></tr>
          <tr><th>MTU</th><td>9000</td></tr>
          <tr><th>Options</th><td>
            <table>
              <tr><th>lacp-rate</th><td>fast</td></tr>
              <tr><th>mode</th><td>802.3ad</td></tr>
              <tr><th>transmit-hash-policy</th><td>layer3+4</td></tr>
            </table>
          </td></tr>
        </table>
    "]
    br_xfer["<p>br_xfer</p>
        <hr/>
        <table>
          <tr><th>Type</th><td>bridge</td></tr>
          <tr><th>MTU</th><td>1500</td></tr>
          <tr><th>Network</th><td>
            <table>
              <tr><th>address</th><td>10.74.0.15/24</td></tr>
              <tr><th>gateway</th><td>10.74.0.1/24</td></tr>
            </table>
          </td></tr>
        </table>
    "]
    br_mgmt["<p>br_mgmt</p>
        <hr/>
        <table>
          <tr><th>Type</th><td>bridge</td></tr>
          <tr><th>MTU</th><td>9000</td></tr>
          <tr><th>Network</th><td>
            <table>
              <tr><th>address</th><td>10.74.20.15/24</td></tr>
            </table>
          </td></tr>
        </table>
    "]
    vlan103["<p>vlan103</p>
        <hr/>
        <table>
          <tr><th>Type</th><td>vlan</td></tr>
          <tr><th>ID</th><td>103</td></tr>
          <tr><th>Network</th><td>
            <table>
              <tr><th>address</th><td>10.74.21.15/24</td></tr>
            </table>
          </td></tr>
        </table>
    "]
    vlan104["<p>vlan104</p>
        <hr/>
        <table>
          <tr><th>Type</th><td>vlan</td></tr>
          <tr><th>ID</th><td>104</td></tr>
          <tr><th>Network</th><td>
            <table>
              <tr><th>address</th><td>10.74.22.15/24</td></tr>
            </table>
          </td></tr>
        </table>
    "]
    vrrp100["<p>vrrp100</p>
        <hr/>
        <table>
          <tr><th>Type</th><td>macvlan</td></tr>
          <tr><th>Address</th><td>00:00:5e:00:01:64</td></tr>
        </table>
    "]

    ens1f0 --> bond_xfer
    ens1f1 --> bond_xfer
    bond_xfer --> br_xfer
    br_xfer --> vrrp100

    ens10f0 --> bond_mgmt
    ens10f1 --> bond_mgmt
    bond_mgmt --> br_mgmt
    br_mgmt --> vlan103
    br_mgmt --> vlan104
```
