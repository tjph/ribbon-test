<template>
	<v-sheet color="#f2f3f6">
	<section id="filter" class="c-donorList">
		<v-container>
		<v-row justify="space-between">
			<v-col cols="auto">
			<h2 class="text-h4">Ribbon Donor List</h2>
			<p class="text-primary mt-3">In Beta now!</p>
			<p class="mt-3">See all those that have given in one place!</p>
			<p>{{ sort_order }}{{ sort_type }}</p>
			</v-col>
		</v-row>
		<v-row>
			<v-col>
			<div class="c-donorList_table elevation-7">
				<input type="text" v-model="search_term" v-on:keyup.enter="searchResult" class="py-3 px-3 ma-5" placeholder="Search by name" />
				<table v-if="donors">
					<thead>
						<tr>
							<th class="text-left py-5 px-5">Name</th>
							<th class="text-left py-5 px-5">Email</th>
							<th class="text-left py-5 px-5" @click="sortResults('total_donations')">
								<div class="sort d-flex align-center">
									<span>Total Donations</span>
									<svg v-if="sort_type === 'total_donations'" xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24"><path d="M7.33 24l-2.83-2.829 9.339-9.175-9.339-9.167 2.83-2.829 12.17 11.996z"/></svg>
								</div>
							</th>
							<th class="text-left py-5 px-5" @click="sortResults('first_donation')">
								<div class="sort d-flex align-center">
									<span>First Donation</span>
									<svg v-if="sort_type === 'first_donation'" xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24"><path d="M7.33 24l-2.83-2.829 9.339-9.175-9.339-9.167 2.83-2.829 12.17 11.996z"/></svg>
								</div>
							</th>
						</tr>
					</thead>
					<tbody>
						<tr v-for="item in donors.data" :key="item.id">
							<td class="py-5 px-5">{{ item.full_name }}</td>
							<td class="c-donorList_table-email py-5 px-5">{{ item.email }}</td>
							<td class="py-5 px-5">{{ item.total_donations }}</td>
							<td class="py-5 px-5">{{ item.first_donation }}</td>
						</tr>
					</tbody>
					<tfoot>
						<tr>
							<th class="text-left py-5 px-5">Rows per page {{ donors.meta.per_page }}</th>
							<th></th>
							<th></th>
							<th class="text-right py-5 px-5">
								<span v-if="donors.links.prev" @click="loadPage(donors.links.prev)">< Prev</span>
								<span v-if="donors.links.next" @click="loadPage(donors.links.next)">Next ></span>
							</th>
						</tr>
					</tfoot>
				</table>
			</div>
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
			donors: null,
			sort_type: null,
			sort_order: null,
			search_term: null,
			page: 1,
			isLoading: false
		};
	},
	computed: {
  	},
	methods: {
		displayEmail (email) {
			const emailArr = email.split('@');
			return `${emailArr[0]}<wbr>@${emailArr[1]}`;
		},
	
		searchResult() {
			console.log('Searching...', this.search_term);
			axios
			.get(`https://interview.ribbon.giving/api/donors?search=${this.search_term}`)
			.then((response) => {
				this.donors = response.data
			});
		},

		sortResults(type) {
			this.sort_type = type;
			this.sort_order = this.sort_order && this.sort_order === 'asc' ? 'desc' : 'asc';
		},

		loadPage(url) {
			console.log('pagination', url);

			let apiUrl = new URL(url);

			if (this.search_term) {
				apiUrl.searchParams.append('search', this.search_term);
			}

			console.log('apiUrl', apiUrl.href);

			// avail sorting & first_donation last_donation search
			axios
			.get(apiUrl.href)
			.then((response) => {
				this.donors = response.data
			});
		}
	},
	mounted () {
		axios
		.get("https://interview.ribbon.giving/api/donors")
		.then((response) => {
			this.donors = response.data
		});
	}
}
</script>

<style lang="scss" scoped>
	.c-donorList_table {
		border-radius: .5rem;
		width: 100%;
		background: #fff;
		color: #3A3A40;

		&-email {
			color: green;
			display: block;
			word-break: break-all;
		}

		.sort {

			svg {
				width: 15px !important;
    			transform: rotate(90deg);
				flex: 0 0 15px;
				margin-left: 8px;
			}
		}

		input {
			border: 2px solid #3A3A40;
			border-radius: 10px;
			width: 40%;
			box-sizing: border-box;

			@media (max-width: 800px) {
				width: calc(100% - 40px);
			}
		}

		table {
			border-collapse: collapse;
			width: 100%;

			tfoot th {
				border: none;
			}
			
			thead {
				background: #3a3a4005;
			}

			th, td {
				border-bottom: 1px solid #EFEFEF;
			}

			td span {
				display: block;
			}
		}
	}
</style>
