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
				<v-form
					v-model="valid"
					validate-on="submit"
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
                    placeholder="Start typing to Search" 
                    return-object
					></v-autocomplete>

					<v-text-field
					v-model="model.id"
					:rules="donorRules"
					readonly
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
export default {
	name: 'DonorMessage',
	components: {
	},
	data() {
		return {
			valid: false,
			entries: [],
			search: null,
    		isLoading: false,
    		model: [ { "email": null, "full_name": null, "id": null } ],
			message: "",
			emailRules: [
				(value) => {
					if (value) return true;

					return "E-mail is required.";
				},
			],
			messageRules: [
				(value) => {
					if (value && value.length > 15) return true;

					return "Message is required (15 characters min).";
				},
			],
			donorRules: [
				(value) => {
					if (value) return true;

					return "Donor Id is required.";
				},
			],
		};
	},
	watch: {
		search (val) {
 			// Items have already been requested
			if (this.isLoading) return

			this.isLoading = true

			// Lazily load input items
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
      		// Send message to server.
			console.log(this.model.email, this.model.full_name, this.model.id, this.message);

    	}
	}
}
</script>

<style lang="scss" scoped>
</style>
