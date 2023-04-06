<template>
	<svg xmlns="http://www.w3.org/2000/svg" class="c-arrowSvg" width="24" height="24" viewBox="0 0 24 24"><path d="M7.33 24l-2.83-2.829 9.339-9.175-9.339-9.167 2.83-2.829 12.17 11.996z"/></svg>
</template>

<script>
export default {
	name: 'ArrowSvg',

}
</script>

<style lang="scss" scoped>
.c-arrowSvg {
	width: 15px !important;
	flex: 0 0 15px;
	fill: #3A3A4087;

	&.page {

		&.prev {
			transform: rotate(-180deg);
		}

		&.next {
			margin-left: 8px;
		}
	}

	&.sort {
		margin-left: 8px;

		&.asc {
			transform: rotate(90deg);
		}

		&.desc {
			transform: rotate(-90deg);
		}
	}
}
</style>

