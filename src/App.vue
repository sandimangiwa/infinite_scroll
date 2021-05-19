<template>
  <div id="app">
    <Header />
    <div class="container-fluid" style="margin-top:90px">
      <div class="row">
        <div class="col-lg-4 mb-4" v-for="data in datas" :key="data.id">
          <div class="card">
            <div class="img-card">
              <img @click="toggleOpen(data)" :src="data.download_url" class="card-img-top" :img-alt="data.author" style="cursor: zoom-in;" />
            </div>
            <div class="card-body">
              <div class="class">
                <div class="d-flex justify-content-between">
                  <h6>{{ data.author }}</h6>
                  <div class="button">
                    <b-button class="mr-1" v-b-tooltip.hover="'download'" variant="success" size="sm" @click="downloadImg(data.download_url, data.author)">
                      <b-icon icon="download"></b-icon>
                    </b-button>
                    <b-button variant="outline-info" v-b-tooltip.hover="'buka ditab baru'" size="sm">
                      <a class="" :href="data.url" target="_blank"><b-icon icon="box-arrow-up-right" aria-hidden="true"></b-icon></a>
                    </b-button>
                  </div>
                </div>

                <div class="ukuran">
                  <small>width: {{ data.width }}</small>
                  <small>height: {{ data.height }}</small>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- modal -->
    <b-modal v-model="isOpen" body-class="p-0 " size="xl" centered hide-footer hide-header>
      <template #default="{ close }">
        <div class="d-flex justify-content-between p-3">
          <h6>{{ dataDetile.author }}</h6>
          <h6 class="m-1" style="cursor: pointer;">
            <b-icon icon="x-circle" scale="2" variant="secondary" @click="close"></b-icon>
          </h6>
        </div>
        <img :src="dataDetile.download_url" alt="" class="img-fluid" />

        <div class="d-flex justify-content-between p-3">
          <div class="ukuran">
            <small>width: {{ dataDetile.width }}</small>
            <small>height: {{ dataDetile.height }}</small>
          </div>

          <div class="button">
            <b-button class="mr-1" v-b-tooltip.hover="'download'" variant="success" size="sm" @click="downloadImg(dataDetile.download_url, dataDetile.author)">
              <b-icon icon="download"></b-icon>
            </b-button>
            <b-button variant="outline-info" v-b-tooltip.hover="'buka ditab baru'" size="sm">
              <a class="" :href="dataDetile.url" target="_blank"><b-icon icon="box-arrow-up-right" aria-hidden="true"></b-icon></a>
            </b-button>
          </div>
        </div>
      </template>
    </b-modal>
  </div>
</template>

<script>
import axios from "axios";
import Header from "@/components/Header";
export default {
  name: "App",
  components: {
    Header,
  },

  data() {
    return {
      datas: [],
      isOpen: false,
      dataDetile: {},
      star: 1,
      end: 20,
    };
  },
  beforeMount() {
    this.getImg();
  },

  mounted() {
    this.infinityScroll();
  },
  methods: {
    getImg() {
      axios
        .get(" https://picsum.photos/v2/list?page=" + this.star + "&limit=" + this.end)
        .then((res) => {
          this.datas = res.data;
          // console.log(res.data);
        })
        .catch((err) => {
          console.log(err);
        });
    },

    infinityScroll() {
      window.onscroll = () => {
        let bottomOfWindow = document.documentElement.scrollTop + window.innerHeight === parseInt(document.documentElement.offsetHeight);
        if (bottomOfWindow) {
          this.end = this.end + 20;

          axios
            .get(" https://picsum.photos/v2/list?page=1&limit=" + this.end)
            .then((res) => {
              this.datas = res.data;
            })
            .catch((err) => {
              console.log(err);
            });
        }
      };
    },

    downloadImg(url, name) {
      console.log(url);
      axios({
        url: url,
        method: "GET",
        responseType: "blob",
      }).then((res) => {
        console.log(res);
        var fileUrl = window.URL.createObjectURL(new Blob([res.data]));
        var filelink = document.createElement("a");
        filelink.href = fileUrl;

        filelink.setAttribute("download", name + ".jpg");
        document.body.appendChild(filelink);
        filelink.click();
      });
    },

    toggleOpen(data) {
      this.dataDetile = data;
      this.isOpen = !this.isOpen;
    },
  },
};
</script>
<style>
.ukuran small {
  color: darkslategray;
  padding: 8px;
  margin-right: 8px;
  background: rgb(221, 220, 220);
}
.img-card {
  width: 100%;
  min-height: 210px;
  max-height: 240px;
  overflow-y: hidden;
  display: flex;
  align-items: center;
}

@media only screen and (max-width: 768px) {
  .img-card {
    width: 100%;
    height: 100%;
    min-height: 100%;
    max-height: 100%;
    overflow-y: hidden;
    display: flex;
    align-items: center;
  }
}
/* Extra large devices (large laptops and desktops, 1200px and up) */
@media only screen and (min-width: 1200px) {
  .img-card {
    width: 100%;

    min-height: 300px;
    max-height: 380px;
    overflow-y: hidden;
    display: flex;
    align-items: center;
  }
}
</style>
