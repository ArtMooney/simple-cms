<template>
  <div class="simple-cms-wrapper">
    <div class="navbar">
      <img
        @click="backHomepage"
        :src="base64image(house)"
        id="w-node-_9debdf39-b234-1d75-2a2c-19226d1fb725-d10df2f5"
        alt=""
        class="cms-back-home"
      /><img
        @click="cmsSettingsMenu = !cmsSettingsMenu"
        :src="base64image(gear)"
        alt=""
        class="cms-account-settings"
      />
      <div
        v-show="cmsSettingsMenu"
        id="w-node-_51417cfc-c8d6-508c-0eea-57308765861a-d10df2f5"
        class="cms-settings-menu"
      >
        <div @click="logOut" class="button bare">Log out</div>
      </div>
    </div>

    <h1 class="text-center">{{ cmsName }}</h1>
    <div>by FrameCore</div>
    <div class="collection-wrapper">
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
    <div class="text-s">Add, edit or remove content below</div>
    <div class="cms-items-block">
      <div @click="addItem()" class="button add-new-item w-button">
        + Add new item
      </div>
    </div>
    <div v-if="!localItems.length" class="cms-items-block">
      <div v-show="!loadingFlag" class="text-s list-message">
        No items found
      </div>
    </div>
    <div class="cms-items-block w-form">
      <div v-if="!appError" class="cms-item-wrapper">
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
              :ref="`list-item-${index}`"
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
                    :src="base64image(gripVertical)"
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
                    :src="base64image(chevronDown)"
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
                        ? 'button controls w-button blinking'
                        : 'button controls w-button',
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
                        ? 'button controls w-button blinking'
                        : 'button controls w-button',
                    ]"
                  >
                    Cancel
                  </div>
                  <div
                    id="w-node-_75f843bd-5bf3-9a52-8a05-85a2c9f5e6dc-d10df2f5"
                    :class="[
                      showItem === index
                        ? 'button controls w-button'
                        : 'button controls hide4 w-button',
                    ]"
                    @click="deleteItem(index)"
                    v-show="currentIndex !== index || !editingNewItem"
                  >
                    Delete
                  </div>
                </div>
                <div
                  id="w-node-_9213f67d-0cf0-9708-3dd0-d741737ab529-d10df2f5"
                  class="item-grid hide4"
                >
                  <img
                    :src="base64image(chevronDown)"
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
      <div v-if="appError" class="error-message">
        <div class="text-s text-black">Sorry, but something went wrong...</div>
      </div>
    </div>
  </div>
</template>

<script>
import { Vue3Lottie } from "vue3-lottie";
import loaderAnim from "../documents/77076-loading.json";
import { Drag, DropList } from "vue-easy-dnd";
import "vue-easy-dnd/dist/dnd.css";
import chevronDown from "../images/chevron-down.svg?raw";
import gripVertical from "../images/grip-vertical.svg?raw";
import house from "../images/house.svg?raw";
import gear from "../images/gear.svg?raw";

export default {
  name: "Cms",
  components: { Vue3Lottie, Drag, DropList },

  data() {
    return {
      schema: [],
      items: [],
      localItems: [],
      userName: "FrameCore",
      userPass: "CMS-development",
      login: {},
      apiBaseUrl: "https://api.framecore.se/webhook/simple-cms/",
      cmsGetBaseSchema: "get-base",
      cmsGetWebhook: "get-items",
      cmsSetWebhook: "set",
      cmsName: "{{ simple }} CMS",
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
      editingNewItem: false,
      appError: false,
      cmsSettingsMenu: false,
      chevronDown: chevronDown,
      gripVertical: gripVertical,
      house: house,
      gear: gear,
    };
  },

  async created() {
    if (this.getLocalStorage("simple-cms-login")) {
      this.login = this.getLocalStorage("simple-cms-login");
    }

    this.schemaIndex = 0;
    this.schema = await this.getCmsData(
      this.apiBaseUrl + this.cmsGetBaseSchema,
      "email=" + this.login.email + "&password=" + this.login.password
    );
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
            // console.log(result);
            resolve(result);
          })
          .catch((error) => {
            // console.log(error);

            this.loadingFlag = false;
            this.appError = true;
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
            // console.log(result);
            resolve(result);
          })
          .catch((error) => {
            // console.log(error);

            this.loadData();
            this.appError = true;
            reject(error);
          });
      });
    },

    async loadData() {
      this.loadingFlag = true;

      this.items = await this.getCmsData(
        this.apiBaseUrl + this.cmsGetWebhook,
        "id=" + this.schema[this.schemaIndex].id
      );
      this.localItems = JSON.parse(JSON.stringify(this.items));

      this.loadingFlag = false;
      this.initLoadedFlag = true;
      this.$emit("initLoadedFlag", true);
    },

    handleClick(event, index) {
      if (this.saveFlag && event.target.nodeName !== "INPUT") {
        const element = this.$refs["list-item-" + this.showItem].$el;

        element.scrollIntoView({
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
      if ((!items && !localItems) || this.editingNewItem) return null;

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

      if (this.editingNewItem) {
        const savedItem = await this.postCmsData(
          this.apiBaseUrl + this.cmsSetWebhook,
          {
            command: "add",
            email: this.login.email,
            password: this.login.password,
            data: [this.getItemJson(index)],
          }
        );

        this.editingNewItem = false;
        this.localItems[index] = savedItem[0];
      } else {
        await this.postCmsData(this.apiBaseUrl + this.cmsSetWebhook, {
          command: "update",
          email: this.login.email,
          password: this.login.password,
          data: [this.getItemJson(index)],
        });
      }

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
      await this.postCmsData(this.apiBaseUrl + this.cmsSetWebhook, {
        command: "update",
        email: this.login.email,
        password: this.login.password,
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

        await this.postCmsData(this.apiBaseUrl + this.cmsSetWebhook, {
          command: "delete",
          email: this.login.email,
          password: this.login.password,
          data: [this.getItemJson(index)],
        });

        this.localItems.splice(index, 1);

        this.items = JSON.parse(JSON.stringify(this.localItems));
        this.savingItemFlag = false;

        this.saveAllItems();
      }, 100);
    },

    addItem() {
      this.editingNewItem = true;
      const index = this.localItems.length;
      this.currentIndex = index;
      let fields = {};

      for (const item of this.schema[0].fields) fields[item.name] = "";
      fields.index = index;

      this.localItems.push({
        createdTime: new Date(),
        fields,
        id: "",
      });

      this.showItem = this.showItem === index ? false : index;

      this.$nextTick(() => {
        this.saveFlag = true;
        const element = this.$refs["list-item-" + this.showItem].$el;
        const inputs = element.querySelectorAll("input");

        element.scrollIntoView({
          behavior: "smooth",
          block: "start",
        });

        inputs[0].focus();
      });
    },

    base64image(image) {
      return `data:image/svg+xml;base64,${btoa(image)}`;
    },

    getLocalStorage(name) {
      const itemStr = localStorage.getItem(name);

      if (!itemStr) {
        return null;
      }

      const item = JSON.parse(itemStr);
      const now = new Date();

      if (now.getTime() > item.expiry) {
        localStorage.removeItem(name);
        return null;
      }
      return item.value;
    },

    deleteLocalStorage(name) {
      localStorage.removeItem(name);
    },

    backHomepage() {
      const protocol = window.location.protocol + "//";
      const siteDomain = window.location.host;

      window.location.href = protocol + siteDomain;
    },

    logOut() {
      this.deleteLocalStorage("simple-cms-login");
      location.reload();
    },
  },

  watch: {
    localItems: {
      deep: true,
      handler(allInputs) {
        if (this.currentIndex === false || this.editingNewItem) return;

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
