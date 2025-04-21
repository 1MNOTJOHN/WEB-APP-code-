import streamlit as st
import random
import time

# -----------------------------
# Function to simulate sensor data
# -----------------------------
def get_sensor_data():
    # Normal driving values
    accel_x = round(random.uniform(-2, 2), 2)
    accel_y = round(random.uniform(-2, 2), 2)
    accel_z = round(random.uniform(9.5, 10.5), 2)  # Gravity included

    # Occasionally simulate a crash
    if random.random() < 0.05:
        accel_x = round(random.uniform(-25, 25), 2)
        accel_y = round(random.uniform(-25, 25), 2)
        accel_z = round(random.uniform(-10, 30), 2)

    return accel_x, accel_y, accel_z

# -----------------------------
# Crash detection based on threshold
# -----------------------------
def detect_crash(ax, ay, az):
    total_accel = (ax**2 + ay**2 + az**2)**0.5
    if total_accel > 30:
        return "CRITICAL"
    elif total_accel > 20:
        return "MINOR"
    else:
        return None

# -----------------------------
# Streamlit app setup
# -----------------------------
st.set_page_config(page_title="SAFE SYNC Crash Monitor", layout="wide")
st.title("🚨 SAFE SYNC - Real-Time Crash Detection")

placeholder = st.empty()

# Simulated live loop
for _ in range(500):  # Limited iterations to avoid infinite loop on web deployment
    ax, ay, az = get_sensor_data()
    status = detect_crash(ax, ay, az)

    with placeholder.container():
        st.subheader("📊 Live Sensor Data")
        st.metric("Accel X (m/s²)", ax)
        st.metric("Accel Y (m/s²)", ay)
        st.metric("Accel Z (m/s²)", az)

        st.subheader("⚠️ Crash Status")
        if status == "CRITICAL":
            st.error("🚨 CRITICAL CRASH DETECTED! Alerting nearby users...")
            st.info("Public Notification: 'Accident detected ahead. Severity: CRITICAL. Please find an alternative route.'")
        elif status == "MINOR":
            st.warning("⚠️ Minor crash detected.")
        else:
            st.success("✅ No crash detected.")

    time.sleep(1)
