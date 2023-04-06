<template>
  <v-app>
    <v-app-bar>
      <v-container class="d-flex align-center py-0" color="#f2f3f6">
        <v-app-bar-title class="pl-0">
          <div class="d-flex align-center">Ribbon</div>
        </v-app-bar-title>
      </v-container>
    </v-app-bar>

    <v-main color="#f2f3f6">
      <section id="hero">
        <v-sheet class="d-flex align-center pb-16" color="#f2f3f6">
          <v-container class="text-center">
            <v-responsive class="mx-auto">
              <h3 class="text-h3">Try Ribbon's all new features</h3>

              <p class="mt-4 text-medium-emphasis">
                Our all-in-one platform gives you the banking, accounting,
                fundraising, and organizational tools you need to build a
                successful charity under the umbrella of your fiscal sponsor.
              </p>
            </v-responsive>
          </v-container>
        </v-sheet>
      </section>

      <DonorsList />

      <DonorMessage />
    </v-main>

    <v-footer>
      <v-container
        class="text-overline d-flex align-center justify-space-between"
      >
        <div>Copyright &copy; 2023 Flourish Change Inc dba Ribbon</div>

        <v-icon icon="mdi-bank" size="x-large" />
      </v-container>
    </v-footer>
  </v-app>
</template>

<script>
import axios from "axios";
import DonorMessage from './components/DonorMessage';
import DonorsList from './components/DonorList';

export default {
  name: "App",
  components: {
    DonorMessage,
    DonorsList
  },

  data() {
    return {
    };
  },
  methods: {
  }
};
</script>
