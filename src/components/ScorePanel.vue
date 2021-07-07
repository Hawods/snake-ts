<template>
	<div id="score-panel">
		<div>SCORE: <span>{{score}}</span></div>
		<div>LEVEL: <span>{{level}}</span></div>
	</div>
</template>

<script lang="ts">
import { defineComponent, inject, ref, toRefs } from 'vue'

export default defineComponent({
  name: 'ScorePanel',
	props: {
		maxLevel: {
			type: Number,
			default: 10,
		},
		levelStep: {
			type: Number,
			default: 10,
		}
	},
	setup(props) {
		const {maxLevel, levelStep} = toRefs(props);
		const	score = ref(0);
		const	level = inject('level', ref(1));

		const reset = () => {
			score.value = 0;
			level.value = 1;
		}

		const addScore = () => {
			score.value++;
			if(level.value < maxLevel.value && score.value % levelStep.value === 0) {
				level.value++;
			}
		};

		return {
			score,
			level,
			reset,
			addScore,
		}
	},
})
</script>

<style lang="less" scoped>
	#score-panel {
		display: flex;
		width: 300px;
		align-items: center;
		justify-content: space-between;
	}
</style>