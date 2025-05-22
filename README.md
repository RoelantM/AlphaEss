# AlphaEss

All credit go to Alex Kroeger, his website is the lead for this
https://projects.hillviewlodge.ie/alphaess/

This is just for personal changes and everyone else that wants to fork from it.

Setup; copied from link

Setup
The following instructions apply to Modbus TCP enabled Inverters only:

Make sure your Inverter is connected via LAN port (not Wi-Fi).
Change your Inverter’s network configuration and assign a static IP Address. Assign an IP Address that’s outside of your main router’s DHCP range to avoid conflicts in the future. Configuring DHCP Reservations on your router can be used alternatively.
Get a HA instance installed, either in a virtual test environment or a HA device.
Install “Samba share” under “Settings/Add-ons/Add-on Store” and configure username and password.
From a computer connect to the HA instance (\\homeassistant) and use the Samba credentials.
Place a copy of integration_alpha_ess.yaml in \\homeassistant\config\packages (create \packages if required).
Edit \\homeassistant\config\configuration.yaml and append:
homeassistant:
  packages: !include_dir_named packages
Edit \\homeassistant\config\secrets.yaml and append (without quotes):
alphaess_modbus_host_ip: "your Inverter’s IP Address"
alphaess_modbus_host_port: 502
alphaess_modbus_slaveId: 85
Perform a restart of your HA.
Get the latest copy of alphaess_view.yaml here:
https://projects.hillviewlodge.ie/_alphaess/alphaess_view.yaml
In HA’s Overview select “Edit Dashboard/Add view/⋮/Edit in YAML” and copy in the content of alphaess_view.yaml. Click “Save”.
Note: on a fresh HA installation you might be missing the “Add view” option (a “+” sign). In that case select “Edit Dashboard/⋮/Take control”, check “Start with an empty dashboard”, then click “Take control”.


 

Configuration
Be careful when making changes and double and triple check everything – writing inconsistent data into your Inverter’s Modbus registers might cause unexpected results which could damage your system!

After making modifications navigate to “Developer Tools”, validate via “Check Configuration” and then reload “All YAML Configuration”.



# AlphaESS Dispatch Info

This document explains the meaning of various AlphaESS dispatch values as seen in the UI. (some assumptions have been made)

---

## Dispatch Parameters

| Parameter                  | Value     | Description |
|----------------------------|-----------|-------------|
| **Dispatch Start**         | `0`       | No dispatch is currently running. If `1`, a dispatch mode (e.g., force discharge) is active. |
| **Dispatch Active Power**  | `0 W`     | No active power is being dispatched. Indicates the battery is neither charging nor discharging. |
| **Dispatch Reactive Power**| `0 W`     | No reactive power is being dispatched. Used for voltage support or power factor correction. |
| **Dispatch Mode**          | `0`       | Mode `0`: Battery only charges from PV. It will not discharge or charge from the grid. |
| **Dispatch SoC**           | `0%`      | Battery State of Charge is at 0%. It is fully depleted. Charging must begin via PV. |
| **Dispatch Time**          | `90 sec`  | Remaining dispatch time or a leftover timer value. Could be a residual value from a prior operation. |

---

## Dispatch Mode Reference

| Mode | Name                        | Description |
|------|-----------------------------|-------------|
| 0    | Battery Only Charges from PV| Battery charges only from PV. Priority: House Load > Battery > Grid Export. No discharge allowed. |
| 2    | State of Charge Control     | Force charge/discharge until a defined SoC target is met. |
| 3    | Load Following              | Self-consumption mode. Matches battery output to house load. |
| 4    | Maximise Output             | Battery discharges to meet inverter output target when PV is insufficient. |
| 5    | Normal Mode                 | Default self-consumption mode. |
| 6    | Optimise Consumption        | PV charges battery first; grid may assist if PV is insufficient. |
| 7    | Maximise Consumption        | Battery charges only from the grid. |
| 19   | No Battery Charge (EMS)     | EMS-restricted self-consumption; charging power does not exceed the set power. |

---

## Notes

- **SoC at 0%** means your system is idle until solar is available.
- To recover from 0%, ensure `Dispatch Mode` is temporarily set to `6` or another charging-friendly mode.
