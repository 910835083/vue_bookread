<template>
  <div class="ebook-reader">
     <div id="read">

     </div>
  </div>
</template>

<script>
import {ebookMixin} from '../../utills/mixin'
import Epub from 'epubjs';
import { getFontFamily, saveFontFamily, getFontSize, saveFontSize, getTheme, saveTheme } from '../../utills/localStorage';
global.ePub = Epub
export default {
    mixins: [ebookMixin],
    methods:{
        prevPage(){
            if(this.rendition){
                this.rendition.prev()
                this.hideTitleAndMenu()
            }
        },
        ToggleTitleAndMenu(){
            if(this.menuVisible){
                this.setSettingVisible(-1)
                this.setFontFamilyVisible(false)
            }
            this.setMenuVisible(!this.menuVisible)
        },
        nextPage(){
            if(this.rendition){
                this.rendition.next()
                this.hideTitleAndMenu()
            }
        },
        hideTitleAndMenu(){
            this.setMenuVisible(false)
            this.setSettingVisible(-1)
            this.setFontFamilyVisible(false)
        },
        initFontSize(){
            let fontSize = getFontSize(this.fileName)
                 if (!fontSize) {
                     saveFontSize(this.fileName, this.defalutFontSize)
                 } else {
                     this.rendition.themes.font(fontSize)
                     this.setDefaultFontSize(fontSize)
                 }
        },
        initFontFamily(){
                 let font = getFontFamily(this.fileName)
                 if (!font) {
                     saveFontFamily(this.fileName, this.defalutFontFamily)
                 } else {
                     this.rendition.themes.font(font)
                     this.setDefaultFontFamily(font)
                 }
        },
        initTheme(){
            let defaultTheme = getTheme(this.fileName)
            if (!defaultTheme) {
                defaultTheme = this.themeList[0].name
                saveTheme(this.fileName, defaultTheme)
            }
            this.setDefaultTheme(defaultTheme)
            this.themeList.forEach(theme => {
                this.rendition.themes.register(theme.name ,theme.style)
            })
            this.rendition.themes.select(defaultTheme)
        },
        initRendition(){
            this.rendition = this.book.renderTo('read' ,{
                 width: innerWidth,
                 height: innerHeight,
                 method: 'default'
             })
            this.rendition.display().then(() => {
                    this.initTheme()
                    this.initFontSize()
                    this.initFontFamily()
                    this.initGlobalStyle()
             })
            this.rendition.hooks.content.register(contents =>{
                 Promise.all(
                     [ 
                 contents.addStylesheet(`${process.env.VUE_APP_RES_URL}/fonts/cabin.css`),
                 contents.addStylesheet(`${process.env.VUE_APP_RES_URL}/fonts/daysOne.css`),
                 contents.addStylesheet(`${process.env.VUE_APP_RES_URL}/fonts/montserrat.css`),
                 contents.addStylesheet(`${process.env.VUE_APP_RES_URL}/fonts/tangerine.css`)
                 ]
                 ).then(() => {
                     console.log('aaa');
                     
                 })
             })
        },
        initGesture(){
            this.rendition.on('touchstart', event => {
                 this.touchStartX = event.changedTouches[0].clientX
                 this.touchStartTime = event.timeStamp
             })
            this.rendition.on('touchend', event => {
                 const offsetX = event.changedTouches[0].clientX - this.touchStartX
                 const time = event.timeStamp - this.touchStartTime
                 if (time<500 && offsetX >40) {
                     this.prevPage()
                 }else if(time<500 && offsetX <-40){
                     this.nextPage()
                 }else{
                     this.ToggleTitleAndMenu()
                 }
                 event.preventDefault();
                 event.stopPropagation()
             })
        },
        initEpub() {
             const url = process.env.VUE_APP_RES_URL + '/epub/' + this.fileName +'.epub'
             this.book = new Epub(url)
             this.setCurrentBook(this.book)
             this.initRendition()
             this.initGesture()
             this.book.ready.then(() => {
                 return this.book.locations.generate(750 * (window.innerWidth / 375) * 
                 (getFontSize(this.fileName) / 16))
             }).then(locations => {
                this.setBookAvailable(true) 
             })
        }
    },
    mounted(){
       this.setFileName(this.$route.params.fileName.split('|').join('/')).then(()=>{
           this.initEpub()
       })

       
    }
}
</script>

<style>

</style>