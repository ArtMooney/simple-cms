<template>
  <div class="simple-cms-wrapper">
    <h1>{{ simple }} CMS</h1>
    <div>by FrameCore</div>
    <div v-show="initLoadedFlag" class="collection-wrapper">
      <div v-for="(table, index) of schema" class="collection-text">
        <div @click="schemaIndex = index">{{ table.name }}</div>
      </div>
    </div>
    <div v-show="initLoadedFlag" class="text-s">
      Add, edit or remove content below
    </div>
    <div class="form-block w-form">
      <div class="cms-item-wrapper">
        <Vue3Lottie
          :animationData="loaderAnim"
          :height="200"
          :width="200"
          v-if="loadingFlag"
        />

        <div
          v-for="(item, index) of localItems"
          @click="handleClick($event, index)"
          class="cms-item"
          v-show="!loadingFlag"
          :ref="'list-item-' + index"
        >
          <div
            id="w-node-ec545091-3119-9423-b023-febb8072a9c9-d10df2f5"
            style="pointer-events: none"
          >
            {{ item.fields.titel }}
          </div>

          <div
            id="w-node-_363c6eb4-ad8c-d7ce-141b-573850873513-d10df2f5"
            class="item-control-wrapper"
          >
            <div
              id="w-node-e9ae915b-0548-dc33-9e2a-f43e8d03efd7-d10df2f5"
              class="loader-wrapper"
              v-show="savingItemFlag && currentIndex === index"
            >
              <div class="loader-anim">
                <Vue3Lottie
                  :animationData="loaderAnim"
                  :height="60"
                  :width="60"
                />
              </div>
            </div>

            <div
              v-if="showItem === index && saveFlag"
              @click="saveItem(index)"
              :class="[
                blinkAnim === true
                  ? 'text-s control-buttons blinking'
                  : 'text-s control-buttons',
              ]"
            >
              Save
            </div>
            <div
              v-if="showItem === index && saveFlag"
              @click="cancelItem(index)"
              :class="[
                blinkAnim === true
                  ? 'text-s control-buttons blinking'
                  : 'text-s control-buttons',
              ]"
            >
              Cancel
            </div>
            <img
              src="./images/chevron-down.svg"
              :class="[
                showItem === index
                  ? 'arrow-down-white rotated w-embed'
                  : 'arrow-down-white w-embed',
              ]"
              id="w-node-_3a59ea88-5010-113e-e088-49750bc9b22a-d10df2f5"
              alt=""
            />
          </div>

          <div
            id="w-node-_7a854a61-d8eb-6699-c242-c06bb6827dc0-d10df2f5"
            class="cms-inputs"
            v-show="showItem === index"
          >
            <div
              id="w-node-_8932dee4-4a00-e945-bd60-da5622cea0d4-d10df2f5"
              class="cms-item-line"
            ></div>

            <template v-for="input of schema[schemaIndex].fields">
              <div class="text-s">
                {{ input.name }}
              </div>
              <input
                @click="handleInput"
                v-model="item.fields[input.name]"
                :type="setCorrectType(input.type)"
                class="cms-input w-input"
                :name="input.name"
                required=""
              />
            </template>
          </div>
        </div>
      </div>
    </div>
    <img src="./images/xmark.svg" alt="" class="cms-close-button" />
  </div>
</template>

<script>
import { Vue3Lottie } from "vue3-lottie";
import loaderAnim from "./documents/77076-loading.json";

export default {
  name: "App",
  components: { Vue3Lottie },

  data() {
    return {
      schema: [],
      items: [],
      localItems: [],
      userName: "FrameCore",
      userPass: "CMS-development",
      cmsGetBaseSchema: "https://api.framecore.se/webhook/get-base-schema",
      cmsGetWebhook: "https://api.framecore.se/webhook/simple-cms-get",
      cmsSetItemWebhook: "https://api.framecore.se/webhook/simple-cms-set",
      simple: "{{ simple }}",
      showItem: false,
      saveFlag: false,
      savingItemFlag: false,
      currentIndex: false,
      schemaIndex: 0,
      loaderAnim,
      loadingFlag: true,
      initLoadedFlag: false,
      blinkAnim: false,
    };
  },

  async created() {
    console.clear();

    this.schemaIndex = 0;
    this.schema = await this.getCmsData(this.cmsGetBaseSchema);
    this.loadData();
  },

  methods: {
    getCmsData(urlEndpoint, options) {
      return new Promise((resolve, reject) => {
        var requestOptions = {
          method: "GET",
          headers: {
            Authorization: "Basic " + btoa(this.userName + ":" + this.userPass),
          },
          redirect: "follow",
        };

        fetch(urlEndpoint + (options ? "?" + options : ""), requestOptions)
          .then((response) => {
            if (!response.ok) throw new Error();
            return response.json();
          })
          .then((result) => {
            console.log(result);
            resolve(result);
          })
          .catch((error) => {
            console.log(error);
            reject(error);
          });
      });
    },

    postCmsData(urlEndpoint, data) {
      return new Promise((resolve, reject) => {
        var requestOptions = {
          method: "POST",
          headers: {
            Authorization: "Basic " + btoa(this.userName + ":" + this.userPass),
          },
          body: this.jsonToFormData(data),
          redirect: "follow",
        };

        fetch(urlEndpoint, requestOptions)
          .then((response) => {
            if (!response.ok) throw new Error();
            return response.json();
          })
          .then((result) => {
            console.log(result);
            resolve(result);
          })
          .catch((error) => {
            console.log(error);

            // must set some kind of error message here as well
            this.loadData();

            reject(error);
          });
      });
    },

    async loadData() {
      this.loadingFlag = true;

      this.items = await this.getCmsData(
        this.cmsGetWebhook,
        "id=" + this.schema[this.schemaIndex].id
      );
      this.localItems = JSON.parse(JSON.stringify(this.items));

      this.loadingFlag = false;
      this.initLoadedFlag = true;
    },

    jsonToFormData(object) {
      const formData = new FormData();
      Object.keys(object).forEach((key) => formData.append(key, object[key]));
      return formData;
    },

    handleClick(event, index) {
      if (this.saveFlag && event.target.nodeName !== "INPUT") {
        this.$refs["list-item-" + this.showItem][0].scrollIntoView({
          behavior: "smooth",
          block: "start",
        });

        this.blinkAnim = true;
        setTimeout(() => {
          this.blinkAnim = false;
        }, 2000);

        return;
      }

      event.target.scrollIntoView({ behavior: "smooth", block: "start" });

      this.showItem = this.showItem === index ? false : index;
      this.currentIndex = index;
    },

    handleInput(event) {
      this.showItem = true;
    },

    isItemChanged(localItems, items) {
      if (!items && !localItems) return null;

      let modified = false;

      for (const [index, input] of Object.entries(localItems.fields)) {
        if (input !== items.fields[index]) modified = true;
      }

      return modified;
    },

    getItemJson(index, localItems, items) {
      if (!localItems && !items) return null;

      let itemJson = {};

      for (const [name, input] of Object.entries(localItems[index].fields)) {
        itemJson[name] = input;
      }

      itemJson.id = items[index].id;
      itemJson.tableid = this.schema[this.schemaIndex].id;

      return itemJson;
    },

    async saveItem(index) {
      this.savingItemFlag = true;
      this.saveFlag = false;
      await this.postCmsData(
        this.cmsSetItemWebhook,
        this.getItemJson(index, this.localItems, this.items)
      );
      this.items = JSON.parse(JSON.stringify(this.localItems));
      this.savingItemFlag = false;
    },

    async cancelItem() {
      this.saveFlag = false;
      this.localItems = JSON.parse(JSON.stringify(this.items));
    },

    setCorrectType(type) {
      let inputType = "text";

      if (type === "multilineText") {
        inputType = "textarea";
      } else if (type === "datum") {
        inputType = "date";
      }

      return inputType;
    },
  },

  watch: {
    localItems: {
      deep: true,
      handler(allInputs) {
        if (this.currentIndex === false) return;

        if (
          this.isItemChanged(
            allInputs[this.currentIndex],
            this.items[this.currentIndex]
          )
        ) {
          this.saveFlag = true;
        } else {
          this.saveFlag = false;
        }
      },
    },

    schemaIndex() {
      this.loadData();
    },
  },
};
</script>
