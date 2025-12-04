<template>
  <div class="container">
    <div class="editor" v-if="selectedSquare">
      <div>
        <label for="numbers">Цифры</label>
        <input type="text" id="numbers" class="numbers-editor" v-model="selectedSquare.numbers" :disabled="!selectedSquare" />
      </div>
      <div>
        <label for="colorNumber">Цвет цифер</label>
        <input type="color" id="colorNumber" class="colors-editor" v-model="selectedSquare.color" :disabled="!selectedSquare" />
      </div>
      <fieldset>
        <legend>Цвет Фона:</legend>
        <div>
          <label for="white">Белый</label>
          <input type="radio" id="white" value="#FFFFFF" v-model="selectedSquare.backgroundColor" :disabled="!selectedSquare" />

          <label for="green">Зеленый</label>
          <input type="radio" id="green" value="#E2EFD9" v-model="selectedSquare.backgroundColor" :disabled="!selectedSquare" />
        </div>
        <div>

        </div>
      </fieldset>
    </div>
    <div class="blocks__wrapper" ref="wrapperRef">
      <div v-for="(item, index) in squares" :key="index" :class="['blocks-item', { active: selectedIndex !== null && selectedIndex === index }]"
           @click="selectIndex(index)" :style="{ backgroundColor: item.backgroundColor }">
        <p>{{ item.direction }}</p>
        <p :style="{ color: item.color }">{{ item.numbers }}</p>
      </div>
    </div>
    <button @click="saveAsPng">Сохранить</button>
  </div>

</template>


<script setup>
import {computed, nextTick, reactive, ref} from "vue";
import html2canvas from "html2canvas";
const selectedIndex = ref(null);
const wrapperRef = ref(null);

const inputText = ref('')
const squares = reactive([
  {direction: 'ЮВ', numbers: '1-3', color: '#333333', backgroundColor: '#FFFFFF'},
  {direction: 'ЮГ', numbers: '2-3', color: '#333333', backgroundColor: '#FFFFFF'},
  {direction: 'ЮЗ', numbers: '3-3', color: '#333333', backgroundColor: '#FFFFFF'},
  {direction: 'Восток', numbers: '4-3', color: '#333333', backgroundColor: '#FFFFFF'},
  {direction: 'центр', numbers: '5-3', color: '#333333', backgroundColor: '#FFFFFF'},
  {direction: 'Запад', numbers: '6-3', color: '#333333', backgroundColor: '#FFFFFF'},
  {direction: 'СВ', numbers: '7-3', color: '#333333', backgroundColor: '#FFFFFF'},
  {direction: 'Север', numbers: '8-3', color: '#333333', backgroundColor: '#FFFFFF'},
  {direction: 'СЗ', numbers: '8-3', color: '#333333', backgroundColor: '#FFFFFF'},
])

const selectedSquare = computed(() => {
  return selectedIndex.value !== null ? squares[selectedIndex.value] : null;
});

const textColor = computed(() => {
  return String(inputText.value);
});

function selectIndex(index) {
  selectedIndex.value = index;
}

async function saveAsPng() {
  selectedIndex.value = null;
  await nextTick();

  const node = wrapperRef.value;
  console.log(node);
  if(!node) {
    alert('Обертка не найдена')
    return
  }

  html2canvas(node, {
    backgroundColor: '#FFFFFF',
    scale: 1,
    useCORS: true
  }).then((canvas) => {
    canvas.toBlob(async (blob) => {
      const fileHandle = await window.showSaveFilePicker({
        suggestedName: 'screenshot.png',
        types: [{
          description: 'PNG Image',
          accept: { 'image/png': [".png"] },
        }],
      })
      const writeble = await fileHandle.createWritable();
      await writeble.write(blob);
      await writeble.close();
    })
  }).catch((err) => {
    console.log(err);
  })

}
</script>
