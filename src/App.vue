<template>
	<router-view />
</template>
<script lang="ts" setup>
	import {
		provide,
		onBeforeMount,
		watch
	} from "vue";
	import router from "./router";
	import store from './store'
	import { setRem } from './public/common'
	provide('store', store)
	provide('router', router)
</script>
<style lang="scss">
	@import './public/common.scss'
</style>
