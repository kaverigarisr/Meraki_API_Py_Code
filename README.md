import meraki

# Replace with your values
API_KEY = 'API-KEY'
ORG_ID = '123456'  # Test Organization ID
NETWORK_NAME = 'Test-Network-WAN'

# Serial numbers of devices you want to add
# MS_SERIAL = '123-4567-8901'
# MR_SERIAL = 'Q345-6789-1011'
MX_SERIAL = 'Q123-6789-8765'
dashboard = meraki.DashboardAPI(API_KEY)

# Step 1: Find the network ID
networks = dashboard.organizations.getOrganizationNetworks(ORG_ID)
target_network = next((n for n in networks if n['name'] == NETWORK_NAME), None)

if not target_network:
    print(f"❌ Network '{NETWORK_NAME}' not found.")
    exit(1)

network_id = target_network['id']
print(f"✅ Found network '{NETWORK_NAME}' with ID: {network_id}")

# Step 2: Add devices to the network ADD # FOR MS_SERIAL & MR_SERIAL IF DOING WAN APPLIANCE
# serials = [MX_SERIAL, MS_SERIAL, MR_SERIAL]
serials = [MX_SERIAL]
try:
    dashboard.networks.claimNetworkDevices(network_id, serials=serials)
    print("✅ Devices claimed successfully.")
except Exception as e:
    print(f"❌ Error claiming devices: {e}")

# Step 3: Name the devices
device_names = {
    MX_SERIAL: "Test-MX",
    #MS_SERIAL: "Test-MS", # please note by adding '#' only MX is being renamed
    #MR_SERIAL: "Test-MR"
}

for serial, name in device_names.items():
    try:
        dashboard.devices.updateDevice(serial, name=name)
        print(f"✅ Renamed device {serial} to '{name}'")
    except Exception as e:
        print(f"❌ Failed to rename device {serial}: {e}")
