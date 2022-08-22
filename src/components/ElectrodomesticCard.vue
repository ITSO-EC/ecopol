<template>
	<button class="mt-2 p-4 flex justify-between bg-gray-300 rounded-lg
			font-semibold capitalize">
		<!-- link -->

		<div class="flex">

			<img class="h-14 w-14 border-2 rounded-md border-indigo-900"
				:src="image ?? 'https://icon-library.com/images/not-found-icon/not-found-icon-18.jpg'">
			<div class="flex flex-col text-left ml-4">

				<span>{{ name }}</span>
				<span class="text-sm text-gray-600">Consumo: {{ power }}W</span>

			</div>

		</div>

		<span>{{ '$' + (parseFloat(avgprice)).toFixed(2) }}</span>

	</button>

</template>

<script>
export default {
	props: ['image', 'name', 'power', 'avgprice']
}
</script>4