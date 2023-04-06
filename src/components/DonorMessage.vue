<template>
	<v-sheet class="py-16" color="#f2f3f6">
		<section id="grid">
			<v-container>
			<v-row justify="space-between">
				<v-col cols="auto">
				<v-responsive width="350">
					<h2 class="text-h4">Show your support feature</h2>
					<p class="text-success mt-3">Available now!</p>
					<p class="mt-3">
					Easily send messages to those that have given!
					</p>
				</v-responsive>
				</v-col>
				<v-sheet width="400" class="mx-auto">
				<div v-if="success">Message sent. Thank you!</div>
				<v-form
					v-model="valid"
					ref="form"
					@submit.prevent="submit"
				>
					<v-textarea
					v-model="message"
					:rules="messageRules"
					label="Message"
					></v-textarea>

					<v-autocomplete
					label="Email"
					v-model="model" 
                    :items="entries" 
                    :loading="isLoading" 
                    :search-input.sync="search"
					:rules="emailRules"
                    item-text="email" 
                    item-value="id" 
                    placeholder="Start typing to Search Email" 
                    return-object
					></v-autocomplete>

					<v-text-field
					v-model="donorId"
					:rules="donorRules"
					label="Donor Id"
					></v-text-field>
					<v-btn type="submit" block class="mt-2">Send</v-btn>
				</v-form>
				</v-sheet>
			</v-row>
			</v-container>
		</section>
	</v-sheet>
</template>

<script>
import axios from "axios";

export default {
	name: 'DonorMessage',
	components: {
	},
	data() {
		return {
			valid: false,
			success: false,
			entries: [],
			search: null,
    		isLoading: false,
    		model: [ { "email": null, "full_name": null, "id": null } ],
			donorId: null,
			message: null,
			emailRules: [
				v => !!v || 'E-mail is required',
				v => v && v.email && /^(([^<>()[\]\\.,;:\s@']+(\.[^<>()\\[\]\\.,;:\s@']+)*)|('.+'))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/.test(v.email) || 'E-mail must be valid'
			],
			messageRules: [
				v => !!v || 'Message is required',
      			v => (v && v.length > 15) || 'Message must be more than 15 characters'
			],
			donorRules: [
				v => !!v || 'Donor Id is required',
			],
		};
	},
	watch: {
		model (val) {
           //watch you code here
		   this.donorId = val?.id || null;
 		},
		search (val) {
 			// Items have already been requested
			if (this.isLoading) return

			this.isLoading = true
			// this.success = false

			// load input items
			fetch(`https://interview.ribbon.giving/api/donors?search=${val}`)
				.then(res => res.json())
				.then(res => {
					console.log(res);
					const { data } = res
					this.entries = data
				})
				.catch(err => {
					console.log(err)
				})
				.finally(() => (this.isLoading = false))
		}
	},
	computed: {
  	},
	methods: {
		async submit() {
			this.success = false;
      		// Send message to server.
			if (this.$refs.form.validate()) {

				await axios({
					method: 'post',
					url: `https://interview.ribbon.giving/api/donors/${this.model.id}/send-message`,
					data: {message: this.message},
					headers: {
						'Content-Type': 'application/json',
					}
				})

				this.success = true;
				this.$refs.form.reset();
			} else {
				this.success = false;
				console.log('FORM ERROR')
			}

    	}
	}
}
</script>

<style lang="scss" scoped>
</style>
