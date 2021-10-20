<template>
  <div>
    <base-dialog :show="!!errorCoaches" title="An error occurred!" @close="handleError">
      <p>{{ errorCoaches }}</p>
    </base-dialog>
    <section>
      <coach-filter @change-filter="setFilters"></coach-filter>
    </section>
    <section>
      <base-card>
        <div class="controls">
          <base-button mode="outline" @click="loadCoaches(true)">Refresh</base-button>
          <base-button v-if="!isCoach && !isLoading" link to="/register">Register as Coach</base-button>
        </div>
        <div v-if="isLoading">
          <base-spinner></base-spinner>
        </div>

        <ul v-else-if="hasCoaches">
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
  </div>
</template>

<script>
import CoachItem from "@/components/coaches/CoachItem.vue";
import CoachFilter from "@/components/coaches/CoachFilter.vue";
import { computed, reactive, ref } from "vue";
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

    const isLoading = ref(false);
    const errorCoaches = ref(null);

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

    const hasCoaches = computed(() => !isLoading.value && store.getters["coaches/hasCoaches"]);

    function setFilters(updatedFilters) {
      activeFilters.frontend = updatedFilters.frontend
      activeFilters.backend = updatedFilters.backend
      activeFilters.career = updatedFilters.career
    }

    async function loadCoaches(refresh = false) {
      isLoading.value = true;
      try {
        await store.dispatch('coaches/loadCoaches', {forceRefresh: refresh});
      } catch (err) {
        errorCoaches.value = err.message || 'Something went wrong!';
      }
      isLoading.value = false;
    }

    function handleError() {
      errorCoaches.value = "";
    }

    loadCoaches();

    return {
      loadCoaches,
      handleError,
      filteredCoaches,
      hasCoaches,
      setFilters,
      isCoach,
      isLoading,
      errorCoaches
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
