<template>
  <div class="container">
    <div class="editor">
      <div>
        <label for="numbers">Цифры</label>
        <input type="text" id="numbers" class="numbers-editor" v-model="squares[selectedIndex].numbers">
      </div>
      <div>
        <label for="colorNumber">Цвет цифер</label>
        <input type="color" id="colorNumber" class="colors-editor" v-model="squares[selectedIndex].color">
      </div>
      <fieldset>
        <legend>Цвет Фона:</legend>
        <div>
          <label for="white">Белый</label>
          <input type="radio" id="white" value="#FFFFFF" v-model="squares[selectedIndex].backgroundColor">

          <label for="green">Зеленый</label>
          <input type="radio" id="green" value="#E2EFD9" v-model="squares[selectedIndex].backgroundColor">
        </div>
        <div>

        </div>
      </fieldset>
    </div>
    <div class="blocks__wrapper" ref="wrapperRef">
      <div v-for="(item, index) in squares" :key="index" :class="['blocks-item', { active: selectedIndex === index }]"
           @click="selectIndex(index)" :style="{ backgroundColor: item.backgroundColor }">
        <p>{{ item.direction }}</p>
        <p :style="{ color: item.color }">{{ item.numbers }}</p>
      </div>
    </div>
    <button @click="saveAsPng">Сохранить</button>
  </div>

</template>


<script setup>
import {computed, reactive, ref} from "vue";
import html2canvas from "html2canvas";
const selectedIndex = ref(0);
const wrapperRef = ref(null);


const inputText = ref('')
const squares = reactive([
  {direction: 'ЮВ', numbers: '1-3', color: '#000000', backgroundColor: '#FFFFFF'},
  {direction: 'ЮГ', numbers: '2-3', color: '#000000', backgroundColor: '#FFFFFF'},
  {direction: 'ЮЗ', numbers: '3-3', color: '#000000', backgroundColor: '#FFFFFF'},
  {direction: 'Восток', numbers: '4-3', color: '#000000', backgroundColor: '#FFFFFF'},
  {direction: 'центр', numbers: '5-3', color: '#000000', backgroundColor: '#FFFFFF'},
  {direction: 'Запад', numbers: '6-3', color: '#000000', backgroundColor: '#FFFFFF'},
  {direction: 'СВ', numbers: '7-3', color: '#000000', backgroundColor: '#FFFFFF'},
  {direction: 'Север', numbers: '8-3', color: '#000000', backgroundColor: '#FFFFFF'},
  {direction: 'СЗ', numbers: '8-3', color: '#000000', backgroundColor: '#FFFFFF'},
])

console.log(squares[selectedIndex.value].color);
const textColor = computed(() => {
  return String(inputText.value);
});

function selectIndex(index) {
  selectedIndex.value = index;
}

function saveAsPng() {
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
