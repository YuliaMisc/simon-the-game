<template>
	<div class="wrapper">
		<div class="container">
			<div class="header">
				<h1>Simon Says</h1>
			</div>
			<div class="content">
				<div class="game">
					<div class="simon">
						<button class="simon_button red" :disabled="!enabled" :class="{ 'active': active === 1, 'enabled': enabled } " id="1" @click="handleClick"></button>
						<button class="simon_button blue" :disabled="!enabled" :class="{ 'active': active === 2, 'enabled': enabled }" id="2" @click="handleClick"></button>
						<button class="simon_button yellow" :disabled="!enabled" :class="{ 'active': active === 3, 'enabled': enabled }" id="3" @click="handleClick"></button>
						<button class="simon_button green" :disabled="!enabled" :class="{ 'active': active === 4, 'enabled': enabled }" id="4" @click="handleClick"></button>
					</div>
				</div>
				<div class="control">
					<div class="game-info">
						<h2>Round: {{ round }}</h2>
						<button @click="startGame">Start</button>
					</div>
					<div v-if="!good" class="gameoff">
						<p> Sorry, you lost after {{ round }}  rounds!</p>
					</div>
					<div class="game-options">
						<h2>Game Options:</h2>
						<label>
							<input type="radio" name="mode" value="easy" @click="setLevel">
							Easy
						</label>
						<label>
							<input type="radio" name="mode" value="normal" @click="setLevel" checked>
							Normal
						</label>
						<label>
							<input type="radio" name="mode" value="hard" @click="setLevel">
							Hard
						</label>
					</div>
				</div>
			</div>
		</div>
	</div>
</template>

<script>
  export default {
    data() {
      return {
        sequence: [],
        playSequence: [],
        round: 0,
				enabled: false,
				active: null,
				interval: 1000,
				good: true,
      };
    },
		methods: {
			startGame() {
				this.sequence = [];
				this.playSequence =[];
				this.round = 0;
				this.good = true;
				this.newRound();
			},

			newRound() {
				this.enabled = false;
				this.round += 1;
				this.sequence.push(this.randomNumber());
				this.playSequence = [];
				this.animate();
			},

			handleClick(e) {
				const item = Number(e.target.id);
				this.activateButton(item);

				this.playSequence.push(item);
				this.check(this.playSequence.length - 1);
			},

			check(key) {
				if (this.sequence[key] === this.playSequence[key]) {
					if (this.sequence.length === this.playSequence.length) {
						this.newRound();
					}
				} else {
					this.gameoff();
				}
			},

			animate() {
				let i = 0;
				const interval = setInterval(() => {
					this.activateButton(this.sequence[i], i === this.sequence.length - 1);
					i+= 1;

					if (i >= this.sequence.length) {
						clearInterval(interval);
					}
				}, this.interval);
			},

			activateButton(item, enable) {
				this.active = item;
				this.playSound();

				setTimeout(() => {
						this.active = null;
						if (enable) {
							this.enabled = true;
						}
					}, this.interval - 200);
			},

			playSound() {
				const audio = document.getElementById(`clip${this.active}`);
				audio.play();
			},

			setLevel(e) {
				switch(e.target.value) {
					case 'easy':
						this.interval = 1700;
					break;
					case 'normal':
						this.interval = 1200;
					break;
					case 'hard':
						this.interval = 600;
					break;
				}
			},

			gameoff() {
				this.good = false;
				this.sequence = [];
				this.playSequence =[];
				this.enabled = false;
			},

			randomNumber() {
				return Math.floor(Math.random() * 4) + 1;
			},
		},
  };
</script>
