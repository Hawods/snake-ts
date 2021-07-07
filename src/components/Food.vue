<template>
	<div v-show="isVisiable" id="food" :style="{top: `${position.y}px`, left: `${position.x}px`}"></div>
</template>

<script lang="ts">
import { defineComponent, inject, onBeforeUnmount, onMounted, reactive, ref } from 'vue'

export default defineComponent({
  name: 'Food',
	setup() {
		const position = inject('foodPosition', reactive({x: 0, y: 0}));
		const isVisiable = ref(false);

		// 变更位置
		const changePosition = () => {
			position.x = Math.floor(Math.random() * 30) * 10;
			position.y = Math.floor(Math.random() * 30) * 10;
		};
		onMounted(() => {
			changePosition();
		});

		// 闪烁
		let visiableHandle: number;
		onMounted(() => {
			visiableHandle = setInterval(() => {
				isVisiable.value = !isVisiable.value;
			}, 500);
		});
		onBeforeUnmount(() => {
			clearInterval(visiableHandle);
		});

		return {
			position,
			isVisiable,
			changePosition,
		}
	},
})
</script>

<style lang="less" scoped>
	#food {
		position: absolute;
		width: 10px;
		height: 10px;
		background-color: #000;
		border: 1px solid #90ee90;
	}
</style>