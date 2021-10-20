<template>
  <div>
    <section>
      <base-card>
        <h2>{{ selectedCoach.firstName }} {{ selectedCoach.lastName }}</h2>
        <h3>${{ selectedCoach.hourlyRate }}/hour</h3>
      </base-card>
    </section>
    <section>
      <base-card>
        <header>
          <h2>Interested? Reach out now!</h2>
          <base-button link :to="contactLink">Contact</base-button>
        </header>
        <router-view></router-view>
      </base-card>
    </section>
    <section>
      <base-card>
        <base-badge v-for="area in selectedCoach.areas" :key="area" :type="area" :title="area"></base-badge>
        <p>{{ selectedCoach.description }}</p>
      </base-card>
    </section>
  </div>
</template>

<script>
import { computed, toRefs } from 'vue';
import { useStore } from 'vuex';
import { useRoute } from 'vue-router'

export default {
  props: ['id'],
  setup(props){
    const { id } = toRefs(props);
    const route = useRoute();
    const store = useStore();
    const selectedCoach = computed(() => store.getters["coaches/coaches"].find(coach => coach.id === id.value));
    const contactLink = computed(() => `${route.path}/contact`);
    return { selectedCoach, contactLink }
  }
}
</script>