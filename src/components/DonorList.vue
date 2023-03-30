<template>
	<v-content>
      <v-sheet color="#f2f3f6">
        <section id="filter" class="c-donorList">
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
				<div class="c-donorList_table">
					<input type="text" placeholder="Search by name" />
					<table v-if="donors">
						<thead>
							<tr>
								<th class="text-left py-5 px-5">Name</th>
								<th class="text-left py-5 px-5">Email</th>
								<th class="text-left py-5 px-5">Total Donations</th>
								<th class="text-left py-5 px-5">First Donation</th>
							</tr>
						</thead>
						<tbody>
							<tr v-for="item in donors.data" :key="item.id">
								<td class="py-5 px-5">{{ item.full_name }}</td>
								<td class="py-5 px-5">{{ item.email }}</td>
								<td class="py-5 px-5">{{ item.total_donations }}</td>
								<td class="py-5 px-5">{{ item.first_donation }}</td>
							</tr>
						</tbody>
						<tfoot>
							<tr>
								<th>Rows per page</th>
								<th></th>
								<th></th>
								<th>{{ donors.links }}</th>
							</tr>
						</tfoot>
					</table>
				</div>
              </v-col>
            </v-row>
          </v-container>
        </section>
      </v-sheet>
	</v-content>
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
		.then((response) => {
			this.donors = response.data
		});
	}
}
</script>

<style scoped>
	.c-donorList_table {
		border-radius: .5rem;
		width: 100%;
		background: #fff;
		color: #3A3A40;
	}

	table {
		border-collapse: collapse;
		width: 100%;
	}

	table thead {
		background: #3a3a4005;
	}

	table th, table td {
		border-bottom: 1px solid #EFEFEF;
	}
</style>
