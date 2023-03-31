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

				<label>
					<svg aria-hidden="true" width="18" height="18" viewBox="0 0 18 18"><path d="m18 16.5-5.14-5.18h-.35a7 7 0 1 0-1.19 1.19v.35L16.5 18l1.5-1.5ZM12 7A5 5 0 1 1 2 7a5 5 0 0 1 10 0Z"></path></svg>
					<input type="text" v-model="search_term" v-on:keyup.enter="searchResult" class="py-3 px-3 ma-5" placeholder="Search by name" />
				</label>

				<v-progress-circular
					v-if="isLoading"
					indeterminate
					color="#00754A"
				></v-progress-circular>

				<table v-if="donors">
					<thead>
						<tr>
							<th class="text-left py-5 px-5">Name</th>
							<th class="text-left py-5 px-5">Email</th>
							<th class="text-left py-5 px-5" @click="sortResults('total_donations')">
								<div class="sort d-flex align-center clickable">
									<span>Total Donations</span>
									<ArrowSvg v-if="sort_type === 'total_donations'" :class="sort_order" class="sort" />
								</div>
							</th>
							<th class="text-left py-5 px-5" @click="sortResults('first_donation')">
								<div class="sort d-flex align-center clickable">
									<span>First Donation</span>
									<ArrowSvg v-if="sort_type === 'first_donation'" :class="sort_order" class="sort" />
								</div>
							</th>
						</tr>
					</thead>
					<tbody>
						<tr v-for="item in donors.data" :key="item.id">
							<td class="py-5 px-5">{{ item.full_name }}</td>
							<td class="c-donorList_table-email py-5 px-5">{{ item.email }}</td>
							<td class="py-5 px-5">{{ item.total_donations }}</td>
							<td class="py-5 px-5">{{ formatDate(item.first_donation) }}</td>
						</tr>
					</tbody>
					<tfoot>
						<tr>
							<th class="text-left py-5 px-5">Rows per page {{ donors.meta.per_page }}</th>
							<th></th>
							<th>
								<span>{{ donors.meta.from }}-{{ donors.meta.to }} of {{ donors.meta.total }}</span>
							</th>
							<th class="text-right py-5 px-5 d-flex justify-end">
								<span v-if="donors.links.prev" @click="loadPage(donors.links.prev)" class="clickable">
									<ArrowSvg class="page prev" />
								</span>
								<span v-if="donors.links.next" @click="loadPage(donors.links.next)" class="clickable">
									<ArrowSvg class="page next" />
								</span>
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
import ArrowSvg from './ArrowSvg.vue';

export default {
	name: 'DonorList',
	components: {
    	ArrowSvg
	},
	data() {
		return {
			donors: null,
			sort_type: null,
			sort_order: null,
			search_term: null,
			page: 1,
			isLoading: false,
			apiUrl: 'https://interview.ribbon.giving/api/donors'
		};
	},
	computed: {
  	},
	methods: {
		formatDate (date) {
			const displayDate = new Date(date);
			return displayDate ? displayDate.toDateString().split(' ').slice(1).join(' ') : '';
		},

		searchResult() {
			if (!this.isLoading) {
				console.log('Searching...', this.search_term);
				this.sort_order = null;
				this.sort_type = null;
				this.loadPage(this.apiUrl);
			}
		},

		sortResults(type) {
			if (!this.isLoading) {
				this.sort_type = type;
				this.sort_order = this.sort_order && this.sort_order === 'asc' ? 'desc' : 'asc';
				this.loadPage(this.apiUrl);
			}
		},

		nextPrevPage(url) {
			if (!this.isLoading) {
				this.loadPage(url);
			}
		},

		loadPage(url) {
			console.log('pagination', url);

			let apiUrl = new URL(url);

			if (this.search_term) {
				apiUrl.searchParams.append('search', this.search_term);
			}

			if (this.sort_type) {
				apiUrl.searchParams.append('sort_by', this.sort_type);
			}

			if (this.sort_order) {
				apiUrl.searchParams.append('sort_direction', this.sort_order);
			}

			console.log('apiUrl', apiUrl.href);

			this.isLoading = true;

			// avail sorting & first_donation last_donation search
			axios
			.get(apiUrl.href)
			.then((response) => {
				this.donors = response.data;
			})
			.catch(error => console.log(error))
			.finally(() => {
				this.isLoading = false;
				this.current_page = this.donors.meta.current_page;
			})
		}
	},
	mounted () {
		if (!this.isLoading) {
			this.loadPage(this.apiUrl);
		}
	}
}
</script>

<style lang="scss" scoped>
	.c-donorList_table {
		border-radius: .5rem;
		width: 100%;
		background: #fff;
		color: #3A3A4087;

		&-email {
			word-break: break-all;
		}

		.clickable {
			cursor: pointer;
			display: flex;
		}

		label {
			position: relative;

			svg {
				position: absolute;
    			top: 50%;
    			left: 35px;
    			transform: translateY(-50%);
				fill: #3A3A4087;
			}
		}

		input {
			border: 2px solid #3A3A4087;
			border-radius: 5px;
			width: 40%;
			box-sizing: border-box;
			padding-left: 45px !important;

			@media (max-width: 800px) {
				width: calc(100% - 100px);
			}
		}

		table {
			border-collapse: collapse;
			width: 100%;
			table-layout: fixed;

			tfoot th {
				border: none;
			}
			
			thead {
				background: #3a3a4005;
				text-transform: uppercase;
			}

			tbody {

				tr:hover {
					background: #3A3A4003;
				}
			}

			th, td {
				border-bottom: 1px solid #3A3A4020;
			}
		}
	}
</style>
