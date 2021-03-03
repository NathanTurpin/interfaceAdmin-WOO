<template>
  <div class="container">
    <div id="app">
      <input type="file" @change="onFileSelected" />

      <div id="preview">
        <img v-if="url" :src="url" />
      </div>
    </div>
    <b-button id="button" @click="onUpload" pill variant="outline-secondary"
          >Upload</b-button
        >

  </div>
</template>

<script>
import test from "@/components/test/test";
import test1 from "@/components/test/test1";
import axios from "axios";
export default {
  components: {
    test,
    test1,
  },
  data() {
    return {
      form: {
        idIMG: 0,
      },
      selectedFile: null,
      url: null,
      token: localStorage.getItem("token"),
      file: true,
    };
  },
  mounted() {},
  methods: {
    onFileSelected(event) {
      this.selectedFile = event.target.files[0];
     this.url = URL.createObjectURL( this.selectedFile );

    },
    onUpload() {
      this.file = true;
      const fd = new FormData();
      fd.append("file", this.selectedFile);
      fd.append("title", this.selectedFile.name);
      const url = window.addresse + "wp-json/wp/v2/media/325";
      axios
        .delete(url, {
          data: {
            force: true,
          },
          headers: {
            Authorization: "Bearer " + this.token,
          },
        })
        .then((res) => console.log(res))
        .catch((error) => {
          console.log(error);
        });
      axios
        .post(window.addresse + "wp-json/wp/v2/media", fd, {
          onUploadProgress: (uploadEvent) => {
            this.uploadPercentage = parseInt(
              Math.round((uploadEvent.loaded / uploadEvent.total) * 100)
            );
          },
          headers: { Authorization: "Bearer " + this.token },
        })
        .then(
          (response) => (
            (this.form.idIMG = response.data.id),
            (this.file = false),
            this.newImg()
          )
        );
    },
    async newImg() {
      console.log(this.form.idIMG);

      const url =
        window.addresse + "/wp-json/wc/v3/products/195/variations/196";
      await axios
        .put(
          url,
          {
            image: {
              id: this.form.idIMG,
            },
          },
          {
            headers: {
              Authorization: "Bearer " + this.token,
            },
          }
        )
        .then((res) => console.log(res))
        .catch((error) => {
          console.log(error);
        });
    },
  },
};
</script>

<style scoped>
.block {
  /* //	display: block;
	//position: relative;
	//width: 295px;
	//border-radius: 5px; */
  background: #fff;
  box-shadow: 0 1px 2px rgba(0, 0, 0, 0.2);
}

.product {
  display: block;
  position: relative;
}

.product img {
  width: 100%;
  border-top-left-radius: 5px;
  border-top-right-radius: 5px;
}

.info {
  display: block;
  position: relative;
  padding: 20px;
}

.details {
  border-top: 1px solid #e5e5e5;
  padding: 18px 20px;
}

.buttons {
  display: block;
  position: absolute;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  border-top-left-radius: 5px;
  border-top-right-radius: 5px;
  background: rgba(255, 255, 255, 0.5);
  opacity: 0;
  -webkit-transition: opacity 0.25s ease-in;
  -ms-transition: opacity 0.25s ease-in;
  -moz-transition: opacity 0.25s ease-in;
  -o-transition: opacity 0.25s ease-in;
  transition: opacity 0.25s ease-in;
}

.product:hover .buttons,
.product:hover a {
  opacity: 1;
}

.buttons a {
  display: block;
  position: absolute;
  left: 50px;
  width: 155px;
  border-radius: 2px;
  /* //padding: 15px 10px 15px 65px;
	//font-family: Helvetica, sans-serif; */

  text-transform: uppercase;
  color: #fff;
  text-decoration: none;
  opacity: 0;
}

a.buy {
  top: 20%;
}

a.preview {
  bottom: 20%;
}

.info::after {
  display: block;
  position: absolute;
  top: -8px;
  left: 23px;
  content: "";
  width: 15px;
  height: 15px;
  background: #fff;
  -webkit-transform: rotate(45deg);
  -ms-transform: rotate(45deg);
  -moz-transform: rotate(45deg);
  -o-transform: rotate(45deg);
  transform: rotate(45deg);
}

.info h4 {
  position: relative;
  padding: 0 0 20px 0;
  margin: 0 0 20px 0;
  font-family: "Open Sans", sans-serif;
  font-weight: 700;
  font-size: 19px;
  line-height: 25px;
  color: #372f2b;
  letter-spacing: -1px;
}

.info h4::after {
  display: block;
  position: absolute;
  bottom: 0px;
  content: "";
  width: 40px;
  height: 2px;
  background: #3b86c4;
}

.info .description {
  display: block;
  padding-bottom: 20px;
  font-family: "Open Sans", sans-serif;
  font-size: 14px;
  font-weight: 600;
  color: #5f5f5f;
}

.info .price {
  font-family: "Open Sans", Helvetica, Arial, sans-serif;
  font-size: 24px;
  font-weight: 700;
  color: #372f2b;
  line-height: 26px;
}

.time {
  font-family: "Open Sans", sans-serif;
  font-size: 14px;
  font-weight: 700;
  color: #372f2b;

  background-position: 0 2px;
}

.rating {
  unicode-bidi: bidi-override;
  direction: rtl;
  font-size: 15px;
}

.rating span.star {
  font-family: FontAwesome;
  font-weight: 400;
  font-style: normal;
  display: inline-block;
}

.rating span.star:hover {
  cursor: pointer;
}

.rating span.star:before {
  content: "\f006";
  padding-right: 5px;
  color: #999;
}

.rating span.star:hover:before,
.rating span.star:hover ~ span.star:before {
  content: "\f005";
  color: #e3cf7a;
}
</style>