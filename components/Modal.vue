<template>
  <div id="custom-modal" class="hidden" @click="closeModalOutside">
    <div id="modal-background" class="fixed left-0 top-0 bottom-0 bg-[black] opacity-70  w-full h-full z-20">
    </div>
    <div id="alp" class="fixed md:top-20 top-4 bottom-0 right-0 left-0 z-20">
      <div class="custom-modal">
        <div @click="watchlist"
             id="modal-watchlist"
             :class="checkWl(1) + 'flex cursor-pointer rounded-tr-xl rounded-bl-xl pr-1 h-[26px] border border-b-[#dbdbdb] border-l-[#dbdbdb] border-r-0 border-t-0 absolute right-0 top-0 text-sm leading-[26px]'">
          <div class="inline-block h-[26px]">
            <img v-show="checkWl(0)" src="/templates/remove.svg" alt="" class="wl-img mt-[2px]" data-do="remove"
                 :data-state="dataState ? 'active' : 'passive' ">
            <img v-show="!checkWl(0)" src="/templates/add.svg" alt="" class="wl-img mt-[2px]" data-do="add"
                 :data-state="!dataState ? 'active' : 'passive'">
          </div>
          <div>
            <span class="">Watchlist</span>
          </div>
        </div>
        <div class="flex p-2 bg-[#EFEFEF] rounded-t-xl border-b border-[#dbdbdb]">
          <div class="md:basis-1/6 basis-0 md:pr-3 pr-0">
            <div>
              <img :src="'/img/' + idProp +'.webp'" alt="" class="rounded-xl">
            </div>
          </div>
          <div class="basis-3/6 md:px-2 px-1">
            <div class="text-[#4a4a4a]">
              <p class="md:text-2xl text-xl font-semibold ubuntu-font ">{{ seriesNameProp }}</p>
              <p class="text-sm">{{ seriesHangProp }}</p>
              <p class="text-lg font-semibold">{{ seriesYearProp }}</p>
              <p v-if="renderCommonProp" id="common-tags" class="text-sm">Common tags:
                <span v-html="coloredTags(commonTagsProp)"></span>
              </p>

            </div>
          </div>
          <div v-if="renderSvgProp" class="md:basis-2/6 basis-3/6 text-center">
            <svg id="svgProgress" height="0" width="0" viewBox="0 0 100 100"
                 class="md:w-[80px] md:h-[80px] w-[50px] h-[50px] md:ml-[calc(calc(100%_-_80px)/2)] ml-[calc(calc(100%_-_50px)/2)]">
              <circle
                  cx="50"
                  cy="50"
                  r="45"
                  stroke="#dbdbdb"
                  stroke-width="6"
                  fill-opacity="0.7"
                  :stroke-dasharray="2*Math.PI*45"
                  :stroke-dashoffset="0"
                  transform="rotate(-90 50 50)"/>
              <circle
                  cx="50"
                  cy="50"
                  r="45"
                  stroke="#C71B2B"
                  fill="transparent"
                  stroke-width="6"
                  :stroke-dasharray="2*Math.PI*40"
                  :stroke-dashoffset="2*Math.PI*40 - (2*Math.PI*45*(rateProp/100))"
                  transform="rotate(-90 50 50)"/>
              <text text-anchor="middle" x="50%" y="60%" fill="#ffc107" font-size="30px">{{ rateProp }}%</text>
            </svg>
            <div class="text-[#4a4a4a]">
              <span>Similar to</span><br>
              <span class="font-semibold">{{ similarNameProp }}</span>
            </div>

          </div>
        </div>

        <div class="flex md:flex-row flex-col p-2">
          <div class="md:basis-1/2 basis-full text-sm overflow-hidden">
            <div v-if="summaryProp">
            <span v-if="summaryProp.length > 550">
            {{ summaryProp.substring(0, 550) }}... <a :href="summaryLinkProp" class="font-semibold hover:underline">Read More</a>
            </span>
              <span v-else>
              {{ summaryProp }}
              <a :href="summaryLinkProp" class="font-semibold hover:underline">Read More</a>
            </span>
            </div>

          </div>
          <div class="md:basis-1/2 basis-full">
            <iframe :src="videoProp" class="w-full aspect-video"/>
          </div>
          <div class="mt-2 md:hidden block">
            <button @click="closeModalOutside"
                    class="bg-[#C7042C] p-1 rounded-[5px]  text-[white] px-4 ml-[calc(calc(100%_-_70px)/2)]">
              Close
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Modal2",
  data() {
    return {
      colors: ["t1", "t2", "t3", "t4", "t5", "t6"],
      dataState: false,
    }
  },

  props: [
    "seriesNameProp",
    "seriesHangProp",
    "seriesYearProp",
    "summaryProp",
    "summaryLinkProp",
    "similarNameProp",
    "rateProp",
    "renderCommonProp",
    "renderSvgProp",
    "videoProp",
    "idProp",
    "commonTagsProp"
  ],

  methods: {

    closeModalOutside(e) {
      if (e.target.id === "modal-background" || e.target.id === "alp" || e.currentTarget.textContent.trim() === "Close") {
        document.querySelector("#custom-modal").style.display = "none"
        document.body.classList.remove("clipped")
        this.videoProp = "";
      }
    },

    coloredTags(param) {
      let coloredTagsArray = []
      param.forEach(tag => {
        let randomColor = this.colors[Math.floor(Math.random() * this.colors.length)]
        const coloredTag = `<span class="tag2 ${randomColor} whitespace-nowrap">${tag}</span>`
        coloredTagsArray.push(coloredTag)
      })
      return coloredTagsArray.join(" ")

    },
    watchlist() {
      const name = `${this.seriesNameProp} (${this.seriesYearProp})`
      let process = null
      document.querySelectorAll(".wl-img").forEach(e => {
        if (e.dataset.state === "active") {
          process = e.dataset.do
        }
      })

      if (process === "add") {
        if (!localStorage.hasOwnProperty("f")) {
          const arr = []
          arr.push(name)
          localStorage.setItem("f", JSON.stringify(arr))
        } else {
          const currentStorage = JSON.parse(localStorage.getItem("f"))
          currentStorage.push(name)
          localStorage.setItem("f", JSON.stringify(currentStorage))
        }
      } else {
        const currentStorage = JSON.parse(localStorage.getItem("f"))
        const arr = currentStorage.filter(e => e !== name)
        localStorage.setItem("f", JSON.stringify(arr))
      }
      this.$store.commit("updateWatchlist")

    },

    checkWl(p) {
      const checkVal = `${this.seriesNameProp} (${this.seriesYearProp})`
      this.$store.commit("checkWatchlist", checkVal)
      this.dataState = this.$store.state.inWatchlist

      if (p === 0) {
        return this.$store.state.inWatchlist;
      } else if (p === 1) {
        return this.$store.state.inWatchlist ? " in-wl hover:bg-[#941521] " : " out-wl hover:bg-[#dbdbdb] "
      }
    }

  }
}
</script>