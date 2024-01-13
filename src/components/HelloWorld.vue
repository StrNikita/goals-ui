<template>
  <v-card>
    <v-tabs v-model="tab" color="deep-purple-accent-4" align-tabs="center">
      <v-tab :value="1">My</v-tab>
      <v-tab :value="2">Create</v-tab>
      <v-tab :value="3">Top up balance</v-tab>
    </v-tabs>
    <v-window v-model="tab">
      <v-window-item :value="1">
        <v-container fluid>
          <v-row>
            <custom-element
              v-for="(item, index) in data"
              :key="index"
              :item="item"
              @itemDeleted="handleItemDeleted"
            ></custom-element>
          </v-row>
        </v-container>
      </v-window-item>

      <v-window-item :value="2">
        <v-container fluid>
          <v-row>
            <v-col cols="12">
              <v-form @submit.prevent="saveData">
                <v-row>
                  <v-col cols="12" md="6">
                    <v-text-field
                      v-model="formData.name"
                      label="Name"
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" md="6">
                    <v-text-field
                      v-model="formData.goal"
                      label="Goal"
                    ></v-text-field>
                  </v-col>
                </v-row>
                <v-btn type="submit" color="primary">Save</v-btn>
              </v-form>
            </v-col>
          </v-row>
        </v-container>
      </v-window-item>

      <v-window-item :value="3">
        <v-container fluid>
          <v-row>
            <v-col cols="12">
              <v-form @submit.prevent="topUpBalance">
                <v-row>
                  <v-col cols="12" md="6">
                    <v-text-field
                      v-model="topupData.hours"
                      label="Hours"
                    ></v-text-field>
                  </v-col>
                </v-row>
                <v-btn type="submit" color="primary">Save</v-btn>
              </v-form>
            </v-col>
          </v-row>
        </v-container>
      </v-window-item>
    </v-window>
  </v-card>
</template>

<script setup lang="ts">
import { ref, onMounted } from "vue";
import CustomElement from "./goal.vue";
import { rate } from "../constants.ts";
const data = ref([]);
const tab = ref(1);

const formData = ref({
  name: "",
  goal: "",
});

const topupData = ref({
  hours: "",
});

const handleItemDeleted = (deletedItemId) => {
  fetchData();
};

const fetchData = async () => {
  try {
    const response = await fetch("http://localhost:3001/goals");
    const result = await response.json();
    data.value = result;
    console.log(data.value);
  } catch (error) {
    console.error("Error fetching data:", error);
  }
};

const saveData = async () => {
  try {
    const response = await fetch("http://localhost:3001/goals", {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify(formData.value),
    });
    const result = await response.json();
    console.log("Data saved:", result);

    fetchData();
  } catch (error) {
    console.error("Error saving data:", error);
  }
};

const topUpBalance = async () => {
  console.log(topupData.value.hours);
  const sum = +topupData.value.hours * rate;
  try {
    const response = await fetch(`http://localhost:3001/balance/${sum}`, {
      method: "PATCH",
      headers: {
        "Content-Type": "application/json",
      },
    });
    const result = await response.json();
    console.log("Data saved:", result);
  } catch (error) {
    console.error("Error saving data:", error);
  }
};

onMounted(() => {
  fetchData();
});
</script>
