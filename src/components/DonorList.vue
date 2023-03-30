<template>
      <v-sheet>
        <section id="filter">
          <v-container>
            <v-row justify="space-between">
              <v-col cols="auto">
                <h2 class="text-h4">Ribbon Donor List</h2>

                <p class="text-primary mt-3">In Beta now!</p>

                <p class="mt-3">See all those that have given in one place!</p>
              </v-col>
            </v-row>
            <v-row>
              <v-col>
                <table v-if="donors">
                  <thead>
                    <tr>
                      <th class="text-left">Name</th>
                      <th class="text-left">Email</th>
                      <th class="text-left">Total Donations</th>
                      <th class="text-left">First Donation</th>
                    </tr>
                  </thead>
                  <tbody>
                    <tr v-for="item in donors.data" :key="item.id">
                      <td>{{ item.full_name }}</td>
                      <td>{{ item.email }}</td>
                      <td>{{ item.total_donations }}</td>
                      <td>{{ item.first_donation }}</td>
                    </tr>
                  </tbody>
                </table>
              </v-col>
            </v-row>
          </v-container>
        </section>
      </v-sheet>
</template>

<script>
import axios from "axios";

export default {
	name: 'DonorList',

	data() {
		return {
		donors: null
		};
	},
	mounted() {
		axios
		.get("https://interview.ribbon.giving/api/donors")
		.then((response) => (this.donors = response.data));
	}
}
</script>
