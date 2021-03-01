<template>
	<div class="wrapper" ref="wrapper">
		<i
			class="fas fa-chevron-left prev"
			:disabled="activeIndex === 0"
			@click="goBack($event)"
		></i>
		<div class="content" ref="content">
			<div
				class="character"
				v-for="(item, index) in characters"
				:key="item.mal_id"
				:style="characterStyle"
				:active="index === activeIndex"
			>
				<character :character="item" :imgWidth="imgWidth"></character>
			</div>
		</div>
		<i
			class="fas fa-chevron-right next"
			:disabled="activeIndex === characters.length - 1"
			@click="goForward($event)"
		></i>
	</div>
</template>

<script>
import Character from './Character';
export default {
	data() {
		return {
			characters: [],
			imgWidth: 225,
			margin: 50,
			activeIndex: null,
			charactersElements: null,
		};
	},

	async created() {
		await this.loadCharacters();
		this.charactersElements = document.querySelectorAll('.character');
		this.adjustSizes();
	},

	methods: {
		async loadCharacters() {
			let characters_staff = await fetch(
				'https://api.jikan.moe/v3/anime/20/characters_staff'
			);
			characters_staff = await characters_staff.json();
			this.characters = [...characters_staff.characters];
		},

		goForward(event) {
			const isDisabled = event.target.getAttribute('disabled');
			if (isDisabled === 'true') return;
			this.charactersElements.forEach(el => {
				const newX =
					Number(el.style.left.slice(0, -2)) - this.imgWidth - this.margin;
				el.style.left = newX + 'px';
			});

			this.activeIndex++;
		},

		goBack(event) {
			const isDisabled = event.target.getAttribute('disabled');
			if (isDisabled === 'true') return;
			document.querySelectorAll('.character').forEach(el => {
				const newX =
					Number(el.style.left.slice(0, -2)) + this.imgWidth + this.margin;
				el.style.left = newX + 'px';
			});

			this.activeIndex--;
		},

		adjustSizes() {
			const wrapperWidth = this.$refs.wrapper.offsetWidth;
			const imgRoom = Math.trunc(wrapperWidth / (this.imgWidth + this.margin));
			this.activeIndex = Math.trunc(imgRoom / 2);
			this.$refs.content.style.width =
				this.imgWidth * imgRoom + this.margin * imgRoom + 'px';
		},
	},

	computed: {
		characterStyle() {
			return {
				'margin-left': this.margin + 'px',
			};
		},
	},

	// watch: {
	// 	activeIndex(curr, prev) {
	// 		this.charactersElements[prev].classList.remove('active');
	// 		this.charactersElements[curr].classList.add('active');
	// 	},
	// },

	components: {
		Character,
	},
};
</script>

<style lang="scss" scoped>
.wrapper {
	background-color: #000;
	position: relative;
}

.content {
	display: flex;
	color: #fff;
	overflow: hidden;
	margin: auto;
	padding: 1rem 0;
}

.character {
	position: relative;
	left: 0;
	transition: 0.5s;
	transform: scale(0.8);

	::v-deep(.info) {
		display: none;
	}

	::v-deep(img) {
		filter: brightness(50%);
	}

	&[active='true'] {
		transform: scale(1);

		::v-deep(.info) {
			display: block;
		}

		::v-deep(img) {
			filter: brightness(100%);
		}
	}
}

.prev,
.next {
	color: #fff;
	font-size: 2.5rem;
	transform: translateY(-50%);

	position: absolute;
	top: 50%;
	&[disabled='true'] {
		color: gray;
	}
}

.prev {
	left: 2%;
}
.next {
	right: 2%;
}
</style>
