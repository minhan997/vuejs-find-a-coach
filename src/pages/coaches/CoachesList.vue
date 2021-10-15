<template>
  <section>
    <coach-filter @change-filter="setFilters"></coach-filter>
  </section>
  <section>
    <base-card>
      <div class="controls">
        <base-button mode="outline">Refresh</base-button>
        <base-button v-if="!isCoach" link to="/register">Register as Coach</base-button>
      </div>
      <ul v-if="hasCoaches">
        <coach-item
          v-for="coach in filteredCoaches"
          :key="coach.id"
          :id="coach.id"
          :first-name="coach.firstName"
          :last-name="coach.lastName"
          :rate="coach.hourlyRate"
          :areas="coach.areas"
        ></coach-item>
      </ul>
      <h3 v-else>No coaches found.</h3>
    </base-card>
  </section>
</template>

<script>
import CoachItem from "@/components/coaches/CoachItem.vue";
import CoachFilter from "@/components/coaches/CoachFilter.vue";
import { computed, reactive } from "vue";
import { useStore } from "vuex";

export default {
  components: {
    CoachItem,
    CoachFilter,
  },
  setup() {
    let activeFilters = reactive({
      frontend: true,
      backend: true,
      career: true,
    });
    const store = useStore();
    const filteredCoaches = computed(() =>
      store.getters["coaches/coaches"].filter((coach) => {
        if (activeFilters.frontend && coach.areas.includes("frontend")) {
          return true;
        }
        if (activeFilters.backend && coach.areas.includes("backend")) {
          return true;
        }
        if (activeFilters.career && coach.areas.includes("career")) {
          return true;
        }
        return false;
      })
    );

    const isCoach = computed(() => store.getters['coaches/isCoach']);

    const hasCoaches = computed(() => store.getters["coaches/hasCoaches"]);

    function setFilters(updatedFilters) {
      activeFilters.frontend = updatedFilters.frontend
      activeFilters.backend = updatedFilters.backend
      activeFilters.career = updatedFilters.career
    }

    return {
      filteredCoaches,
      hasCoaches,
      setFilters,
      isCoach
    };
  },
};
</script>

<style scoped>
ul {
  list-style: none;
  margin: 0;
  padding: 0;
}

.controls {
  display: flex;
  justify-content: space-between;
}
</style>