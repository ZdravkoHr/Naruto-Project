<template>
	<div class="character-img">
		<img :src="imgUrl" :alt="name" :width="imgWidth" />
	</div>

	<div class="info">
		<p class="name">{{ name }}</p>
	</div>
</template>

<script>
export default {
	props: ['character', 'imgWidth'],
	data() {
		return {
			imgUrl: null,
			name: null,
		};
	},
	mounted() {
		this.imgUrl = this.character.image_url;
		this.name = this.character.name;
	},
};
</script>

<style lang="scss" scoped></style>
