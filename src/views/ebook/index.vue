<template>
  <div class="ebook">
      <EbookTitle></EbookTitle>
      <EbookReader></EbookReader>
      <EbookMenu></EbookMenu>
  </div>
</template>

<script>
import EbookReader from '../../components/ebook/EbookReader'
import EbookMenu  from '../../components/ebook/EbookMenu'
import EbookTitle from '../../components/ebook/EbookTitle';
export default {
  components : {
    EbookReader,
    EbookTitle,
    EbookMenu
  }
}
</script>

<style>

</style>