import React, { useState, useEffect } from "react"; import { StyleSheet, Text, View, TouchableOpacity, ScrollView } from "react-native"; import { Pedometer } from "expo-sensors"; import { useKeepAwake } from "expo-keep-awake"; import * as Notifications from "expo-notifications"; import * as Location from "expo-location"; import AsyncStorage from "@react-native-async-storage/async-storage";

export default function App() { useKeepAwake(); const [date, setDate] = useState(new Date()); const [steps, setSteps] = useState(0); const [distance, setDistance] = useState(0); const [calories, setCalories] = useState(0);

useEffect(() => { const timer = setInterval(() => setDate(new Date()), 1000); return () => clearInterval(timer); }, []);

useEffect(() => { let subscription; (async () => { const isAvailable = await Pedometer.isAvailableAsync(); if (isAvailable) { subscription = Pedometer.watchStepCount(result => { setSteps(result.steps); const dist = result.steps * 0.8; // rata-rata 0.8 m per langkah setDistance(dist / 1000); setCalories(result.steps * 0.04); }); } })(); return () => subscription && subscription.remove(); }, []);

return ( <ScrollView style={styles.container} contentContainerStyle={{ flexGrow: 1 }}> <View style={styles.clockContainer}> <Text style={styles.time}>{date.toLocaleTimeString()}</Text> <Text style={styles.date}>{date.toDateString()}</Text> </View>

<View style={styles.statsContainer}>
    <Text style={styles.label}>Langkah: {steps}</Text>
    <Text style={styles.label}>Jarak: {distance.toFixed(2)} km</Text>
    <Text style={styles.label}>Kalori: {calories.toFixed(0)} kcal</Text>
  </View>

  <TouchableOpacity style={styles.button}>
    <Text style={styles.buttonText}>Set Alarm</Text>
  </TouchableOpacity>
</ScrollView>

); }

const styles = StyleSheet.create({ container: { flex: 1, backgroundColor: "#0b1220", // tema dark elegan padding: 20, }, clockContainer: { marginTop: 50, alignItems: "center", }, time: { fontSize: 60, fontWeight: "bold", color: "#1976D2", // biru elegan (brand color) }, date: { fontSize: 20, color: "#FFC107", // amber lembut marginTop: 10, }, statsContainer: { marginTop: 40, backgroundColor: "#121212", borderRadius: 20, padding: 20, }, label: { fontSize: 22, color: "#FAFAFA", marginBottom: 10, }, button: { marginTop: 40, backgroundColor: "#1976D2", padding: 15, borderRadius: 15, alignItems: "center", }, buttonText: { fontSize: 20, color: "white", fontWeight: "bold", }, });

