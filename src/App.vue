<script setup>
import { onMounted, reactive, ref } from 'vue';
import Grid from './components/Grid.vue';
import Menu from './components/Menu.vue';



let ldArray = reactive([])
let rdArray = reactive([])
let bArray = []
let tArray = []
let time = 0
let bombs = 10
let col = 7
let row = 7
const gridKey = ref(0)
const fromChild = ref()
const faceState = ref('')
const active = ref(false)
let gameStop = false
let interval 
let bomb_count = bombs

function numberToArray(number) {
	let array = []
	number = number.toString()
	array = number.split('')
	if(array.length < 2){
		array.unshift('0', '0')
	}else if(array.length < 3){
		array.unshift('0')
	}
	return array
}

function createDisplayUrlArray(number ,array, sarray) {
	array = numberToArray(number)
	if(array.length > 3){
		return
	}else{
		for(let i = 0;i < array.length; i++){
			switch(array[i]){
				case '0':
            	sarray[i] = new URL (`../src/assets/img/0-info.png`, import.meta.url)
            	break
				case '1':
            	sarray[i] = new URL (`../src/assets/img/1-info.png`, import.meta.url)
            	break
				case '2':
            	sarray[i] = new URL (`../src/assets/img/2-info.png`, import.meta.url)
            	break
				case '3':
            	sarray[i] = new URL (`../src/assets/img/3-info.png`, import.meta.url)
            	break
				case '4':
            	sarray[i] = new URL (`../src/assets/img/4-info.png`, import.meta.url)
            	break
				case '5':
            	sarray[i] = new URL (`../src/assets/img/5-info.png`, import.meta.url)
            	break
				case '6':
            	sarray[i] = new URL (`../src/assets/img/6-info.png`, import.meta.url)
            	break
				case '7':
            	sarray[i] = new URL (`../src/assets/img/7-info.png`, import.meta.url)
            	break
				case '8':
            	sarray[i] = new URL (`../src/assets/img/8-info.png`, import.meta.url)
            	break
				case '9':
            	sarray[i] = new URL (`../src/assets/img/9-info.png`, import.meta.url)
            	break
			}
		}
	}
}

function gameWon() {
	faceState.value = "won"
	gameStop = true
	bomb_count = 0
	createDisplayUrlArray(bombs, bArray, ldArray)
}

function gameLost() {
	faceState.value = 'lost'
	gameStop = true
}

function mouseDown() {
	if(gameStop === false){
		faceState.value = 'o-o'
	}
}

function mouseUp() {
	if(gameStop === false){
		faceState.value = ''
	}
}

function faceClick() {
	faceState.value = 'click'
}

function faceUnClick() {
	faceState.value = ''
}

function changeFace() {
	switch(faceState.value){
		case 'won':
		return new URL ('../src/assets/img/chill-face.png', import.meta.url)
		break
		case 'lost':
		return new URL ('../src/assets/img/dead-face.png', import.meta.url)
		break
		case 'o-o':
		return new URL ('../src/assets/img/o-o-face.png', import.meta.url)
		break
		case 'click':
		return new URL ('../src/assets/img/happy-face-clicked.png', import.meta.url)
		break		
		default:
		return new URL ('../src/assets/img/happy-face.png', import.meta.url)
	}
}

function updateTime(){
	time++
	createDisplayUrlArray(time, tArray, rdArray)
}
function listenPlay() {
	updateTime()
	return interval = setInterval(() => updateTime(), 1000)
}
function listenPause() {
	return clearInterval(interval)
}

function listenInflag() {
	bomb_count--
	createDisplayUrlArray(bomb_count, bArray, ldArray)
}
function listenOutflag() {
	bomb_count++
	createDisplayUrlArray(bomb_count, bArray, ldArray)
}

function toggleMenu() {
	active.value = !active.value
}

function difficulty(dif) {
	switch(dif){
		default:
			col = 7
			row = 7
			bombs = 10
			reloadGrid()
	}

}

function reloadGrid() {
	faceState.value = ''
	gameStop = false
	time = 0
	bomb_count = bombs
	gridKey.value++
	fromChild.value.gameInit()
	listenPause()
	createDisplayUrlArray(bombs, bArray, ldArray)
	createDisplayUrlArray(time, tArray, rdArray)
}

onMounted(() => {
	createDisplayUrlArray(bombs, bArray, ldArray)
	createDisplayUrlArray(time, tArray, rdArray)
})

</script>

<template>
	<div class="flex justify-center h-screen">
		<div class="flex flex-col justify-center">
			<div id="window" class="flex flex-col w-fit h-fit shadow-window bg-gray-win p-px scale-100">
				<div id="game-window" class="flex flex-col w-fit h-fit self-center">
					<div id="top" class="flex w-full h-[3px] bg-white justify-end">
						<img src="../src/assets/img/side-win.png" alt="side-shadow" class="w-[3px] h-[3px]">
					</div>
					<div id="middle" class="flex">
						<div id="right" class="w-[3px] h-full bg-white"></div>
						<div id="game-box" class="flex flex-col bg-gray-win">
							<div id="game-header" class="flex flex-col m-1.5 mb-0">
								<div id="top" class="flex w-full h-0.5 bg-dark-gray-win justify-end">
									<img src="../src/assets/img/lside-win.png" alt="lside-shadow" class="h-0.5 w-0.5">
								</div>
								<div id="middle" class="flex">
									<div id="right" class="w-0.5 h-full bg-dark-gray-win"></div>
									<div id="display" class="flex justify-between w-full">
										<div id="left-diplay" class="flex w-[41px] h-[25px] bg-display my-1 ml-1.5 p-px">
											<img :src="image" v-for="image in ldArray" class="w-[13px] h-[23px]">
										</div>
										<button @click="reloadGrid()" >
											<img 
											:src="changeFace()" 
											@mousedown.left="faceClick()" 
											@mouseup.left="faceUnClick()" 
											@touchstart="faceClick()"
											@touchend="faceUnClick()" alt="face-button" class="mt-px">
										</button>
										<div id="left-right" class="flex w-[41px] h-[25px] bg-display my-1 mr-1.5 p-px">
											<img :src="image" v-for="image in rdArray" class="w-[13px] h-[23px]">
										</div>
									</div>
									<div id="left" class="w-0.5 h-full bg-white"></div>
								</div>
								<div id="bottom" class="w-full h-0.5 bg-white">
									<img src="../src/assets/img/lside-win.png" alt="lside-shadow" class="h-0.5 w-0.5">
								</div>
							</div>
							<div id="game-body" class="flex flex-col m-1.5">
								<div id="top" class="flex w-full h-[3px] bg-dark-gray-win justify-end">
									<img src="../src/assets/img/side-win.png" alt="side-shadow" class="w-[3px] h-[3px] rotate-180">
								</div>
								<div id="middle" class="flex">
									<div id="right" class="w-[3px] h-full bg-dark-gray-win"></div>
									<Grid 
									:bombs="bombs" 
									:col="col" 
									:row="row"
									:key="gridKey"
									ref="fromChild" 
									@play.once="listenPlay" 
									@pause.once="listenPause" 
									@flaged="listenInflag" 
									@unflaged="listenOutflag"
									@win="gameWon"
									@lose="gameLost"
									@gameInit="reloadGrid"
									@mousedown.left="mouseDown"
									@mouseup.left="mouseUp"
									@touchstart="mouseDown"
									@touchend="mouseUp"
									></Grid>
									<div id="left" class="w-[3px] h-full bg-white"></div>
								</div>
								<div id="bottom" class="w-full h-[3px] bg-white">
									<img src="../src/assets/img/side-win.png" alt="side-shadow" class="w-[3px] h-[3px] rotate-180">
								</div>
							</div>
						</div>
						<div id="left" class="w-[3px] h-full bg-dark-gray-win"></div>
					</div>
					<div id="bottom" class="w-full h-[3px] bg-dark-gray-win" >
						<img src="../src/assets/img/side-win.png" alt="side-shadow" class="w-[3px] h-[3px]">
					</div>
				</div>
			</div>
		</div>
	</div>
</template>

<style scoped>
div, img {
  image-rendering: pixelated;
  user-select: none;
}
</style>
