<template>
  <div>
    <base-dialog :show="!!errorRequest" title="An error occurred!" @close="handleError">
      <p>{{ errorRequest }}</p>
    </base-dialog>
    <section>
      <base-card>
        <header>
          <h2>Requests Received</h2>
        </header>
        <base-spinner v-if="isLoading"></base-spinner>
        <ul v-else-if="hasRequests && !isLoading">
          <request-item
            v-for="req in receivedRequests"
            :key="req.id"
            :email="req.userEmail"
            :message="req.message"
          ></request-item>
        </ul>
        <h3 v-else>You haven't received any requests yet!</h3>
      </base-card>
    </section>
  </div>
</template>
<script>
import RequestItem from "@/components/requests/RequestItem.vue";
import { computed, ref } from "vue";
import { useStore } from "vuex";

export default {
  components: {
    RequestItem,
  },
  setup() {
    const store = useStore();
    const isLoading = ref(false);
    const errorRequest = ref(null);

    const receivedRequests = computed(() => store.getters["requests/requests"]);
    const hasRequests = computed(() => store.getters["requests/hasRequests"]);
    loadRequests();

    async function loadRequests() {
      isLoading.value = true;
      try {
        await store.dispatch("requests/fetchRequests");
      } catch (error) {
        errorRequest.value = error.message || "Something failed!";
      }
      isLoading.value = false;
    }

    function handleError() {
      errorRequest.value = "";
    }

    return {
      receivedRequests,
      hasRequests,
      handleError,
      errorRequest,
      isLoading,
    };
  },
};
</script>
