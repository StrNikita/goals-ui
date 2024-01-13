<template>
  <v-col cols="12" md="4">
    <v-card class="goal-card p-3">
      <div class="card-header">
        <v-card-title>{{ item.name }}</v-card-title>

        <img
          v-if="calculateProgress(item) === 100"
          src="../assets/cat.gif"
          alt="GIF"
          class="goal-gif"
        />

        <v-btn @click="deleteItem" icon color="error" class="delete-btn">
          <v-icon>mdi-delete</v-icon>
        </v-btn>
      </div>
      <v-card-subtitle>{{ item.goal }}</v-card-subtitle>

      <v-progress-linear
        v-model="skill"
        color="primary"
        :model-value="calculateProgress(item)"
        height="25"
      >
        <template v-slot:default="{ value }">
          <strong>{{ calculateProgress(item) }}%</strong>
        </template>
      </v-progress-linear>
    </v-card>
  </v-col>
</template>

<style scoped>
.card-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.delete-btn {
  width: 30px !important;
  height: 30px !important;
  justify-self: end !important;
}

.goal-gif {
  width: 50px;
  height: auto;
}
</style>

<script setup lang="ts">
import { ref } from "vue";
const props = defineProps(["item"]);

const calculateProgress = (item) => {
  console.log(item.currentMoney, item.goal);
  return (item.currentMoney / item.goal) * 100;
};

const item = ref(props.item);

const deleteItem = async () => {
  try {
    const itemId = item._value.id;

    const response = await fetch(`http://localhost:3001/goals/${itemId}`, {
      method: "DELETE",
    });

    if (response.ok) {
      console.log("Item deleted successfully from the backend");
    } else {
      console.error("Failed to delete item from the backend");
    }
  } catch (error) {
    console.error("Error deleting item:", error);
  }
};
</script>
