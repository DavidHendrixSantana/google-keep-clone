<template>
  <div class="modal">
    <div class="modal-content" :style="{ backgroundColor: selectedNote.color }">
      <input class="modal-title"  :style="{backgroundColor : selectedNote.color}" placeholder="Title" type="text" v-model="selectedNote.title" />   
      <input class="modal-text" :style="{backgroundColor : selectedNote.color}"  placeholder="Take a note..." type="text" v-model="selectedNote.content" />   
      <div class="color-picker">
        <div class="color-option" v-for="color in colors" :key="color" :style="{ backgroundColor: color }" @click="selectColor(color)"></div>
      </div>
      <span class="modal-close-button" @click="closeModal">Close</span>
    </div>    
  </div>  
  <main>
    <header>
      <img src="https://www.gstatic.com/images/branding/product/1x/keep_48dp.png">
      <h2>keep</h2>
    </header>
    <div id="form-container">
      <form id="form" autocomplete="off" @submit.prevent="saveNote">
        <input v-model="note.title" id="note-title" type="text" placeholder="Title" v-show="showtTitle">
        <input v-model="note.content" id="note-text" type="text" placeholder="Take a note...">
        <div id="form-buttons" v-show="showButtons">
          <button type="submit" id="submit-button">Submit</button>
          <button type="button" id="close-button" ref="closeButton">Close</button>
        </div>
      </form>
    </div>
    <div id="notes">
      <div id="placeholder">
        <p v-if="notes.length === 0" id="placehoilder-text">Notes you add appear here</p>
        <div v-else class="note" v-for="note in notes" :key="note.id" @click="openModal(note)" :style="{ backgroundColor: note.color }">
          <div class="note-text">{{ note.title }}</div>
          <div class="note-title">{{ note.content }}</div>
          <div class="toolbar-container">
            <div class="toolbar">
              <img class="toolbar-delete" src="delete.png" width="10px"  @click.stop="deleteNote(note.id)">
            </div>
          </div>
        </div>
      </div>
    </div>
  </main>
</template>

<script lang="ts"  setup>
import {  ref,Ref, onMounted, onUnmounted } from 'vue'
import INote from '@/interfaces/INote'
const showButtons = ref(false)
const showtTitle = ref(false)
const selectedNote:Ref<INote> = ref({
  id: 1,
  title: "",
  content: "",
  color: ""
})
const note:Ref<INote> = ref({
  id: 1,
  title: "",
  content: "",
  color: "white"
})
const notes:Ref<Array<INote>> = ref([])
const colors = ref(["#ffffff", "#d7aefb", "#fbbc04", "#a7ffeb"])

const openForm = () =>{
  showButtons.value = true
  showtTitle.value = true
}
const closeForm = () =>{
  showButtons.value = false
  showtTitle.value = false
}
const handleClick = (event: MouseEvent) =>{
  const form = document.getElementById('form')
  const isFormClicked = form.contains(event.target)
  if(isFormClicked ){
    openForm()
  }else if( note.value.title || note.value.content){
    saveNote()
  }else{
    closeForm()

  }
}

const handleCloseButtonClick = (event: MouseEvent) => {
  event.stopPropagation()
  closeForm()
}

const saveNote = ():void =>{

  notes.value.push(note.value)
  note.value = {
    id:  note.value.id + 1,
    title:"",
    content :"",
    color:""
  }
}

const editNote = ():void =>{
  const index = notes.value.findIndex((n) => n.id === selectedNote.value.id)
  notes.value[index] = selectedNote.value
}
const deleteNote = (id: number) => {
  notes.value = notes.value.filter(note => note.id !== id)
}

const openModal =(note:INote) =>{
  selectedNote.value = note
  const modal = document.querySelector('.modal')
  modal?.classList.add('open-modal')

}

const closeModal = () =>{
  editNote()
  const modal = document.querySelector('.modal')
  modal?.classList.remove('open-modal')
}

const selectColor = (color: string) => {
  selectedNote.value.color = color
}

onMounted(()=>{
  document.addEventListener('click', handleClick)
  const closeButton = document.getElementById('close-button')
  closeButton?.addEventListener('click', handleCloseButtonClick)
})
onUnmounted(() => {
  document.removeEventListener('click', handleClick)
  const closeButton = document.getElementById('close-button')
  closeButton?.removeEventListener('click', handleCloseButtonClick)
})
</script>

<style scoped>
* {
  margin: 0;
  padding: 0;
  font-family: "Google Sans", sans-serif;
  box-sizing: border-box;
}

html,
body {
  background-color: #fff;
  color: #202124;
  font-size: 15px;
  margin: 0;
  padding: 0;
}

main {
  margin: 0 auto;
  max-width: 900px;
}

#placeholder {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  align-items: flex-start;
  margin-top: 10vh !important;
}

#placeholder-logo {
  height: 120px;
  margin: 20px;
  opacity: 0.1;
  width: 120px;
}

#placeholder-text {
  color: #80868b;
  cursor: default;
  font-size: 1.375rem;
  font-weight: 400;
  line-height: 1.75rem;
}

#form-container {
  border-radius: 8px;
  border: 0.5px solid #d3d3d3;
  box-shadow: 0 1px 2px 0 rgba(60, 64, 67, 0.302),
    0 2px 6px 2px rgba(60, 64, 67, 0.149);
  margin: 32px auto 16px auto;
  max-width: 496px;
  transition-duration: 0.218s;
  transition-property: background, border, opacity, box-shadow, transform;
  transition-timing-function: ease-in;
}

#form {
  position: relative;
  border: none;
  margin: 1px;
  border-radius: 8px;
  transition-duration: 0.218s;
  transition-property: background, border, opacity, box-shadow, transform;
  transition-timing-function: ease-in;
}

.form-open {
  box-shadow: 0 3px 5px rgba(0, 0, 0, 0.2);
}

header {
  display: flex;
  align-items: center;
  justify-content: center;
  margin-top: 2em;
}

.header-logo {
  height: 4em;
}

.header-title {
  color: #5f6368;
  font-size: 2rem;
  padding-top: 4px;
  padding-left: 4px;
}

#notes {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
}

#note-text {
  width: calc(100% - 10px);
  min-height: 43px;
  margin: 0 5px;
  padding: 10px 10px;
  font-size: 1rem;
  font-weight: 500;
  line-height: 1.5rem;
  /* box-shadow: 0 3px 5px rgba(0, 0, 0, 0.2); */
  letter-spacing: 0.00625em;
  border: 0;
  overflow: hidden;
  position: relative;
}

.modal-title {
  width: calc(100% - 10px);
  min-height: 43px;
  margin: 0 5px;
  font-weight: 500;
  line-height: 1.5rem;
  letter-spacing: 0.00625em;
  border: 0;
  overflow: hidden;
  position: relative;
  font-size: 1.375rem;
  font-weight: 400;
  line-height: 1.75rem;
  padding-bottom: 12px;
  padding-top: 16px;
}

.modal-text {
  width: calc(100% - 10px);
  min-height: 43px;
  margin: 0 5px;
  padding: 10px 10px;
  font-size: 1rem;
  font-weight: 500;
  line-height: 1.5rem;
  letter-spacing: 0.00625em;
  border: 0;
  overflow: hidden;
  position: relative;
  color: #202124;
  font-size: 14px;
  line-height: 19px;
  min-height: 46px;
  padding: 4px 16px 12px 0px;
}

#note-title {
  width: calc(100% - 10px);
  min-height: 43px;
  margin: 0 5px;
  padding: 10px 10px;
  font-size: 1rem;
  font-weight: 500;
  line-height: 1.5rem;
  border: 0;
}

#form:focus,
input:focus {
  outline: none;
}


#form-buttons {
  text-align: right;
}

#submit-button,
#close-button {
  margin: 0.2em 0;
  box-sizing: border-box;
  color: rgba(0, 0, 0, 0.87);
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
  letter-spacing: 0.01785714em;
  font-size: 0.875rem;
  font-weight: 500;
  line-height: 1.25rem;
  height: 36px;
  padding: 8px 24px;
  border-radius: 4px;
  border-color: transparent !important;
  background-color: rgb(251, 251, 251);
}

#submit-button {
  color: #fff;
  background-color: #007bff;
}
#close-button {
  color: #fff;
  background-color: #949494;
}

#submit-button:hover,
#form-close-button:hover {
  filter: brightness(95%);
}

.note {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  margin: 16px;
  width: 250px;
  background-color: #fff;
  border-color: #e0e0e0;
  border: 1px solid #d3d3d3;
  box-sizing: border-box;
  overflow: hidden;
  position: relative;
  border-radius: 8px;
  transition-duration: 0.218s;
  transition-property: background, border, opacity, box-shadow, transform;
  transition-timing-function: ease-in;
}

.note:hover {
  box-shadow: 0 1px 2px 0 rgba(60, 64, 67, 0.302),
    0 1px 3px 1px rgba(60, 64, 67, 0.149);
}

.note-title {
  padding-top: 12px;
  letter-spacing: 0.00625em;
  font-size: 1rem;
  font-weight: 500;
  line-height: 1.5rem;
  min-height: 38px;
  padding: 16px 16px 0 16px;
  transition: background-color 0.218s ease-in;
  box-sizing: border-box;
  font-variant-ligatures: none;
  outline: none;
  white-space: pre-wrap;
  word-wrap: break-word;
}

.note-text {
  font-size: 1.125rem;
  font-weight: 400;
  line-height: 1.5rem;
  min-height: 30px;
  letter-spacing: 0.01428571em;
  padding: 4px 16px 12px 16px;
  box-sizing: border-box;
  font-variant-ligatures: none;
  outline: none;
  white-space: pre-wrap;
  word-wrap: break-word;
}

.toolbar {
  opacity: 0;
  flex-direction: row-reverse;
  align-items: center;
  color: rgba(0, 0, 0, 0.54);
  display: flex;
  font-size: 12px;
  line-height: 26px;
  margin: 4px 0;
  position: relative;
  transition: opacity 0.218s ease-in-out, background-color 0.218s ease-in-out,
    box-shadow 0.218s ease-in-out;
}

.toolbar:hover {
  opacity: 1;
}

.toolbar-color,
.toolbar-delete {
  height: 20px;
  margin: 0 8px;
  width: 20px;
  margin: 0 3px;
  cursor: pointer;
  color: #202124;
  opacity: 0.71;
}

.toolbar-color-modal {
  display: none;
}

#color-tooltip {
  border-bottom: 2px solid #fff;
  position: absolute;
  top: -40;
  background: #fff;
  border: 1px solid black;
  left: 0;
  z-index: 20;
  border-radius: 5px;
  display: none;
  box-shadow: 0 1px 4px rgba(0, 0, 0, 0.2);
  padding: 5px 7px 3px;
  width: 136px;
  height: 40px;
  justify-content: space-between;
}

.color-option {
  width: 25px;
  height: 25px;
  border-radius: 50%;
  border: 1px solid #000000;
}

.color-option:hover {
  border: 1px solid black;
}

.modal {
  position: fixed;
  left: 0;
  z-index: 200;
  top: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(229, 229, 229, 0.5);
  opacity: 0;
  visibility: hidden;
  transform: scale(1.1);
  transition: visibility 0s linear 0.25s, opacity 0.25s 0s, transform 0.25s;
}

.modal-content {
  box-shadow: 0 1px 3px 0 rgba(60, 64, 67, 0.302),
    0 4px 8px 3px rgba(60, 64, 67, 0.149);
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background-color: white;
  padding: 1rem 1.5rem;
  width: 24rem;
  border-radius: 0.5rem;
}

.modal-close-button {
  float: right;
  margin: 0.2em 0;
  box-sizing: border-box;
  color: rgba(0, 0, 0, 0.87);
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
  letter-spacing: 0.01785714em;
  font-size: 1rem;
  font-weight: 500;
  line-height: 1.25rem;
  height: 36px;
  padding: 8px 24px;
  border-radius: 4px;
  border-color: transparent !important;
  background-color: rgb(251, 251, 251);
  cursor: pointer;
}

.modal-close-button:hover {
  background-color: rgba(95, 99, 104, 0.039);
}

.open-modal {
  opacity: 1;
  visibility: visible;
  transform: scale(1);
  transition: visibility 0s linear 0s, opacity 0.25s 0s, transform 0.25s;
}

#white {
  background: white;
}

#purple {
  background: #d7aefb;
}

#orange {
  background: #fbbc04;
}

#teal {
  background: #a7ffeb;
}

.color-picker {
  display: flex;
  margin-top: 10px;
}

</style>
