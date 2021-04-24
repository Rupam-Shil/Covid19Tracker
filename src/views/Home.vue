<template>
	<main v-if="!loading">
		<DataTitle :text="title" :dataDate="dataDate" />
		<DataBoxes :stats="stats" />
		<CountrySelect :countries="countries" @get-country="getCountryData" />
		<button
			v-if="title !== 'Global'"
			@click="clearData"
			class="bg-green-700 text-white rounded p-3 mt-10 focus:outline-none hover:bg-green-600"
		>
			Clear Country
		</button>
	</main>
	<main class="flex flex-col align-center justify-center text-center" v-else>
		<div class="text-grey-500 text-3xl mt-10 mb-6">
			Fetching Data
		</div>
		<img :src="loadingImage" alt="Timer" class="w-24 m-auto" />
	</main>
</template>

<script>
import DataTitle from '../components/DataTitle';
import DataBoxes from '../components/DataBoxes';
import CountrySelect from '../components/CountrySelect';
export default {
	name: 'Home',
	data() {
		return {
			loading: true,
			title: 'Global',
			dataDate: '',
			stats: {},
			countries: [],
			loadingImage: require('../assets/hourglass.gif'),
		};
	},
	components: { DataTitle, DataBoxes, CountrySelect },
	methods: {
		async fetchCovidData() {
			const res = await fetch('https://api.covid19api.com/summary');
			const data = await res.json();
			return data;
		},
		getCountryData(country) {
			this.stats = country;
			this.title = country.Country;
		},
		async clearData() {
			this.loading = true;
			const data = await this.fetchCovidData();
			this.title = 'Global';
			this.dataDate = data.Date;
			this.loading = false;
			this.stats = data.Global;
		},
	},
	async created() {
		const data = await this.fetchCovidData();
		this.dataDate = data.Date;
		this.stats = data.Global;
		this.countries = data.Countries;
		this.loading = false;
	},
	emits: ['get-country'],
};
</script>
