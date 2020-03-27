<template>
  <div class="ebook-reader">
     <div id="read">

     </div>
  </div>
</template>

<script>
import {ebookMixin} from '../../utills/mixin'
import Epub from 'epubjs';
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
        initEpub() {
             const url = 'http://192.168.1.195:8081/epub/' + this.fileName +'.epub'
             this.book = new Epub(url)
             this.setCurrentBook(this.book)
             this.rendition = this.book.renderTo('read' ,{
                 width: innerWidth,
                 height: innerHeight,
                 method: 'default'
             })
             this.rendition.display()
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