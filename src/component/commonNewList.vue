<template>
	<div class="commonNewList" v-for="(item,index) in data" @click="communityClick(item)">
		<img v-if="item.image_path" :src="item.image_path?item.image_path:require('../assets/image/icon-defalut.png')" @error="(e)=>{e.target.src = require('../assets/image/icon-defalut.png')}" @click="communityClick(item)" >
		<div class="common-rect-list-right" :style="{width:!item.image_path?'100%!important':''}">
			<h3>{{item.title}}</h3>
			<p v-html="item.content.replace(/\s*/g,'')" class="common-rect-list-right-dec"></p>
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
	.commonNewList{
		width: 98%;
		height: 80px;
		background: white;
		border-radius: 5px;
		padding: 7px  0;
		display: flex;
		justify-content: flex-start;
		flex-direction: row;
		align-items: center;
		margin-bottom: 20px;
		cursor: pointer;
		box-sizing: border-box;
		position: relative;
		&:after{
			content: "";
			position:absolute;
			bottom: 0;
			right: 0;
			width: 100%;
			height: 1px;
			background-image: linear-gradient(to right,rgba(145,149,160,0.1),rgba(145,149,160,0.2),rgba(145,149,160,0.1));
		}
		img{
			width: 60px;
			height: 60px;
			border-radius: 5px;
			margin-right: 10px;
			object-fit: cover;
		}
		.common-rect-list-right{
			width: calc(100% - 70px);
			height: 100%;
			position: relative;
			h3{
				font-size: 13px;
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
				-webkit-line-clamp: 1;
				overflow: hidden;
				margin: 0;
				line-height: 20px;
			}
			span{
				font-size: 12px;
				color: #9094a0;
				position: absolute;
				bottom: 0;
				left: 0;
			}
		}
	}
</style>
