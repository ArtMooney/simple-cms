<template>
  <div class="simple-cms-wrapper">
    <h1>{{ simple }} CMS</h1>
    <div>by FrameCore</div>
    <div v-show="initLoadedFlag" class="collection-wrapper">
      <div v-for="(table, index) of schema">
        <div
          @click="schemaIndex = index"
          :class="[
            schemaIndex === index
              ? 'collection-text chosen'
              : 'collection-text',
          ]"
        >
          {{ table.name }}
        </div>
      </div>
    </div>
    <div v-show="initLoadedFlag" class="text-s">
      Add, edit or remove content below
    </div>
    <div class="cms-items-block">
      <div class="text-s button-add-new">+ Add new item</div>
    </div>
    <div class="cms-items-block w-form">
      <div class="cms-item-wrapper">
        <Vue3Lottie
          :animationData="loaderAnim"
          :height="200"
          :width="200"
          v-if="loadingFlag"
        />

        <drop-list
          :items="localItems"
          class="cms-items"
          @reorder="
            $event.apply(localItems);
            saveAllItems();
          "
        >
          <template v-slot:item="{ item, index }">
            <drag
              :delay="dragDelay"
              :vibration="dragVibration"
              @click.prevent="handleClick($event, index)"
              class="cms-item"
              v-show="!loadingFlag"
              :id="'list-item-' + index"
              :key="item"
              handle=".dragdrop-handle"
            >
              <div
                id="w-node-_5f4e042c-c275-1c4b-7191-70138bfa36f8-d10df2f5"
                class="item-title-wrapper"
              >
                <div
                  id="w-node-e97159e2-8ebc-f0c7-089d-ad3d67bfd80a-d10df2f5"
                  class="item-title-wrapper"
                >
                  <img
                    src="./images/grip-vertical.svg"
                    id="w-node-_3152d18a-1e62-e7da-3fba-64d3117471a9-d10df2f5"
                    alt=""
                    class="dragdrop-handle"
                  />
                  <div
                    id="w-node-ec545091-3119-9423-b023-febb8072a9c9-d10df2f5"
                    style="pointer-events: none"
                  >
                    {{ item.fields.titel }}
                  </div>
                </div>
                <div class="item-grid show4">
                  <img
                    src="./images/chevron-down.svg"
                    v-if="
                      (!savingItemFlag ||
                        (savingItemFlag && currentIndex !== index)) &&
                      !savingAllItemsFlag
                    "
                    id="w-node-ca2ac5e0-9092-1cea-f525-5ac925d9f1af-d10df2f5"
                    alt=""
                    :class="[
                      showItem === index
                        ? 'arrow-down-white rotated w-embed'
                        : 'arrow-down-white w-embed',
                    ]"
                  />

                  <div
                    id="w-node-ca2ac5e0-9092-1cea-f525-5ac925d9f1b0-d10df2f5"
                    class="loader-wrapper"
                    v-show="
                      (savingItemFlag && currentIndex === index) ||
                      savingAllItemsFlag
                    "
                  >
                    <div class="loader-anim">
                      <Vue3Lottie
                        :animationData="loaderAnim"
                        :height="60"
                        :width="60"
                      />
                    </div>
                  </div>
                </div>
              </div>

              <div
                id="w-node-_363c6eb4-ad8c-d7ce-141b-573850873513-d10df2f5"
                :class="[
                  showItem === index
                    ? 'item-control-wrapper'
                    : 'item-control-wrapper hide4',
                ]"
              >
                <div
                  id="w-node-_1d27ccc1-0f7f-00b3-b75f-85f2d1900c66-d10df2f5"
                  class="item-grid"
                >
                  <div
                    id="w-node-_0b476731-d8fb-2341-5ad4-24f0f7c9aac8-d10df2f5"
                    v-if="showItem === index && saveFlag"
                    @click="saveItem(index)"
                    :class="[
                      blinkAnim === true
                        ? 'text-s button-controls blinking'
                        : 'text-s button-controls',
                    ]"
                  >
                    Save
                  </div>
                  <div
                    id="w-node-b1b7d981-a5e9-7138-58d8-3d72cfc97804-d10df2f5"
                    v-if="showItem === index && saveFlag"
                    @click="cancelItem(index)"
                    :class="[
                      blinkAnim === true
                        ? 'text-s button-controls blinking'
                        : 'text-s button-controls',
                    ]"
                  >
                    Cancel
                  </div>
                  <div
                    id="w-node-_75f843bd-5bf3-9a52-8a05-85a2c9f5e6dc-d10df2f5"
                    :class="[
                      showItem === index
                        ? 'text-s button-controls'
                        : 'text-s button-controls hide4',
                    ]"
                    @click="deleteItem(index)"
                  >
                    Delete
                  </div>
                </div>
                <div
                  id="w-node-_9213f67d-0cf0-9708-3dd0-d741737ab529-d10df2f5"
                  class="item-grid hide4"
                >
                  <img
                    src="./images/chevron-down.svg"
                    v-if="
                      (!savingItemFlag ||
                        (savingItemFlag && currentIndex !== index)) &&
                      !savingAllItemsFlag
                    "
                    :class="[
                      showItem === index
                        ? 'arrow-down-white rotated w-embed'
                        : 'arrow-down-white w-embed',
                    ]"
                    id="w-node-_3a59ea88-5010-113e-e088-49750bc9b22a-d10df2f5"
                  />
                  <div
                    id="w-node-e9ae915b-0548-dc33-9e2a-f43e8d03efd7-d10df2f5"
                    class="loader-wrapper"
                    v-show="
                      (savingItemFlag && currentIndex === index) ||
                      savingAllItemsFlag
                    "
                  >
                    <div class="loader-anim">
                      <Vue3Lottie
                        :animationData="loaderAnim"
                        :height="60"
                        :width="60"
                      />
                    </div>
                  </div>
                </div>
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
                  <div v-if="input.name !== 'index'" class="text-s">
                    {{ input.name }}
                  </div>
                  <input
                    v-if="input.name !== 'index'"
                    @click="handleInput"
                    v-model="item.fields[input.name]"
                    :type="setCorrectType(input.type)"
                    class="cms-input w-input"
                    :name="input.name"
                    required=""
                  />
                </template>
              </div>
            </drag>
          </template>
          <template v-slot:feedback="{ data }"> </template>
        </drop-list>
      </div>
    </div>
    <img src="./images/xmark.svg" alt="" class="cms-close-button" />
  </div>
</template>

<script>
import { Vue3Lottie } from "vue3-lottie";
import loaderAnim from "./documents/77076-loading.json";
import { Drag, DropList } from "vue-easy-dnd";
import "vue-easy-dnd/dist/dnd.css";

export default {
  name: "App",
  components: { Vue3Lottie, Drag, DropList },

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
      savingAllItemsFlag: false,
      currentIndex: false,
      schemaIndex: 0,
      loaderAnim,
      loadingFlag: true,
      initLoadedFlag: false,
      blinkAnim: false,
      dragDelay: 0,
      dragVibration: 100,
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
            "Content-Type": "application/json",
            Authorization: "Basic " + btoa(this.userName + ":" + this.userPass),
          },

          body: JSON.stringify(data),
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

    handleClick(event, index) {
      if (this.saveFlag && event.target.nodeName !== "INPUT") {
        // this.$refs["list-item-" + this.showItem][0].scrollIntoView({
        document.getElementById("list-item-" + this.showItem).scrollIntoView({
          behavior: "smooth",
          block: "start",
        });

        this.blinkAnim = true;
        setTimeout(() => {
          this.blinkAnim = false;
        }, 2000);

        return;
      }

      // if delete is clicked, do not change showItem panel
      if (event.target.innerText !== "Delete") {
        event.target.scrollIntoView({ behavior: "smooth", block: "start" });
        this.showItem = this.showItem === index ? false : index;
      }

      this.currentIndex = index;
    },

    handleInput(event) {
      this.showItem = true;
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

    isItemChanged(localItems, items) {
      if (!items && !localItems) return null;

      let modified = false;

      for (const [index, input] of Object.entries(localItems.fields)) {
        if (input !== items.fields[index]) modified = true;
      }

      return modified;
    },

    getItemJson(index) {
      let itemJson = {};
      itemJson = JSON.parse(JSON.stringify(this.localItems[index].fields));
      itemJson.id = this.localItems[index].id;
      itemJson.tableid = this.schema[this.schemaIndex].id;

      return itemJson;
    },

    async saveItem(index) {
      this.savingItemFlag = true;
      this.saveFlag = false;
      await this.postCmsData(this.cmsSetItemWebhook, {
        command: "update",
        data: [this.getItemJson(index)],
      });
      this.items = JSON.parse(JSON.stringify(this.localItems));
      this.savingItemFlag = false;
    },

    async saveAllItems() {
      const itemArray = [];
      this.savingAllItemsFlag = true;
      this.saveFlag = true;

      for (const [index, item] of Object.entries(this.localItems)) {
        item.fields.index = index;
        itemArray.push(this.getItemJson(index));
      }

      // max 10 items per api call according to airtable but maybe test this at some point
      await this.postCmsData(this.cmsSetItemWebhook, {
        command: "update",
        data: itemArray,
      });
      this.items = JSON.parse(JSON.stringify(this.localItems));
      this.savingAllItemsFlag = false;
      this.saveFlag = false;
    },

    async cancelItem() {
      this.saveFlag = false;
      this.localItems = JSON.parse(JSON.stringify(this.items));
    },

    deleteItem(index) {
      this.savingItemFlag = true;
      this.saveFlag = false;

      setTimeout(async () => {
        this.showItem = false;

        await this.postCmsData(this.cmsSetItemWebhook, {
          command: "delete",
          data: [this.getItemJson(index)],
        });

        this.localItems.splice(index, 1);

        this.items = JSON.parse(JSON.stringify(this.localItems));
        this.savingItemFlag = false;
      }, 100);
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

    showItem() {
      if (this.showItem === false) {
        this.dragDelay = 0;
      } else {
        this.dragDelay = 86400000;
      }
    },
  },
};
</script>

<style scoped>
.text-s.button-controls {
  pointer-events: auto;
}

.item-control-wrapper {
  pointer-events: none;
}

@keyframes blink {
  0% {
    background-color: #8a548b;
  }
  50% {
    background-color: white;
  }
  100% {
    background-color: #8a548b;
  }
}

.blinking {
  animation: blink 700ms 2;
}

.cms-item {
  cursor: pointer;
}
</style>
