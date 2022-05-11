<template>
	<div class="common-rect-list" v-for="(item,index) in data" @click="communityClick(item)">
		<img v-if="item.image_path" :src="item.image_path?item.image_path:require('../assets/image/icon-defalut.png')" @error="(e)=>{e.target.src = require('../assets/image/icon-defalut.png')}" @click="communityClick(item)" >
		<div class="common-rect-list-right" :style="{width:!item.image_path?'100%!important':''}">
			<h3>{{item.title}}</h3>
			<p v-html="item.content.replace(/<[^>]+>/g, '')" class="common-rect-list-right-dec"></p>
			<span>{{item.create_time}}</span>
		</div>
	</div>
</template>

<script lang="ts" setup>
	import {
		ref
	} from 'vue'
	import {
		useRouter
	} from 'vue-router'
	const router = useRouter();
	const emits = defineEmits(['delete'])
	const props = defineProps({
		data: Array,
		deleteShow: Boolean
	})
	const communityClick = (item) => {
		router.push({
			path: '/communityDetail',
			query: {
				id: item.id
			}
		})
	}
	const deleteClick = (id) => {
		emits("delete", id)
	}
</script>

<style lang="scss">
	.common-rect-list{
		width: 98%;
		height: 140px;
		background: white;
		border-radius: 5px;
		padding: 16px;
		display: flex;
		justify-content: flex-start;
		flex-direction: row;
		align-items: center;
		margin-bottom: 20px;
		cursor: pointer;
		box-sizing: border-box;
		img{
			width: 100px;
			height: 100px;
			border-radius: 5px;
			margin-right: 20px;
			object-fit: cover;
		}
		.common-rect-list-right{
			width: calc(100% - 84px - 20px);
			height: 100%;
			position: relative;
			h3{
				font-size: 16px;
				color: var(--main-font-color);
				display: -webkit-box;
				-webkit-box-orient: vertical;
				-webkit-line-clamp: 1;
				overflow: hidden;
				margin: 0;
				margin-bottom: 10px;
			}
			p{
				margin: 0;
			}
			.common-rect-list-right-dec{
				font-size: 14px;
				color: #60616c;
				display: -webkit-box;
				-webkit-box-orient: vertical;
				-webkit-line-clamp: 3;
				overflow: hidden;
				margin: 0;
				line-height: 20px;
				
			}
			span{
				font-size: 13px;
				color: #9094a0;
				position: absolute;
				bottom: 0;
				left: 0;
			}
		}
	}
</style>
