<template>
  <div @click="handleClickOutside" class="simple-cms-wrapper">
    <div class="navbar">
      <house
        @click="backHomepage"
        id="w-node-_9debdf39-b234-1d75-2a2c-19226d1fb725-d10df2f5"
        style="color: white"
        class="cms-back-home"
      />
      <gear
        @click="cmsSettingsMenu = !cmsSettingsMenu"
        style="color: white"
        class="cms-account-settings"
      />
      <div
        v-show="cmsSettingsMenu"
        id="w-node-_51417cfc-c8d6-508c-0eea-57308765861a-d10df2f5"
        class="cms-settings-menu"
      >
        <div @click="logOut" class="cms-button bare">Log out</div>
      </div>
    </div>

    <div class="title-wrapper">
      <h1 class="cms-title">{{ cmsName }}</h1>
      <div class="text-s">by FrameCore</div>
    </div>

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

    <div class="cms-items-menu">
      <div @click="addItem()" class="cms-button add-new-item w-button">
        + Add new item
      </div>
      <div class="relative">
        <div @click.stop="sortItems" class="cms-button sort-by-date w-button">
          Sort list by date
        </div>

        <div v-if="showDateList" class="datelist-wrapper">
          <div class="text-s">Choose date field</div>
          <div class="datelist-content">
            <div v-if="getDateList().length < 1" class="text-s datelist">
              No date fields
            </div>
            <div
              v-for="date of getDateList()"
              @click="sortDateField"
              class="text-s datelist"
            >
              {{
                date.name.includes("|") ? date.name.split("|")[0] : date.name
              }}
            </div>
          </div>
        </div>
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
              @click="handleClick($event, index)"
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
                  <gripVertical
                    id="w-node-_3152d18a-1e62-e7da-3fba-64d3117471a9-d10df2f5"
                    style="color: white"
                    class="dragdrop-handle"
                  />
                  <div
                    id="w-node-ec545091-3119-9423-b023-febb8072a9c9-d10df2f5"
                    style="pointer-events: none"
                  >
                    {{ item.titel }}
                  </div>
                </div>
                <div class="item-grid show4">
                  <chevronDown
                    v-if="
                      (!savingItemFlag ||
                        (savingItemFlag && currentIndex !== index)) &&
                      !savingAllItemsFlag
                    "
                    id="w-node-ca2ac5e0-9092-1cea-f525-5ac925d9f1af-d10df2f5"
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
                    @click.stop="saveItem(index, item)"
                    :class="[
                      blinkAnim === true
                        ? 'cms-button controls w-button blinking'
                        : 'cms-button controls w-button',
                    ]"
                  >
                    Save
                  </div>
                  <div
                    id="w-node-b1b7d981-a5e9-7138-58d8-3d72cfc97804-d10df2f5"
                    v-if="showItem === index && saveFlag"
                    @click.stop="cancelItem(index)"
                    :class="[
                      blinkAnim === true
                        ? 'cms-button controls w-button blinking'
                        : 'cms-button controls w-button',
                    ]"
                  >
                    Cancel
                  </div>
                  <div
                    id="w-node-_75f843bd-5bf3-9a52-8a05-85a2c9f5e6dc-d10df2f5"
                    :class="[
                      showItem === index
                        ? 'cms-button controls w-button'
                        : 'cms-button controls hide4 w-button',
                    ]"
                    @click.stop="deleteItem(index)"
                    v-show="currentIndex !== index || !editingNewItem"
                  >
                    Delete
                  </div>
                </div>
                <div
                  id="w-node-_9213f67d-0cf0-9708-3dd0-d741737ab529-d10df2f5"
                  class="item-grid hide4"
                >
                  <chevronDown
                    v-if="
                      (!savingItemFlag ||
                        (savingItemFlag && currentIndex !== index)) &&
                      !savingAllItemsFlag
                    "
                    id="w-node-_3a59ea88-5010-113e-e088-49750bc9b22a-d10df2f5"
                    :class="[
                      showItem === index
                        ? 'arrow-down-white rotated w-embed'
                        : 'arrow-down-white w-embed',
                    ]"
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
                    {{
                      input.name.includes("|")
                        ? input.name.split("|")[0]
                        : input.name
                    }}
                  </div>
                  <input
                    v-if="
                      input.name !== 'index' &&
                      getInputType(input.type) !== 'textarea' &&
                      getInputType(input.type) !== 'file' &&
                      getInputType(input.type) !== 'date' &&
                      getInputType(input.type) !== 'checkbox' &&
                      !isToFromType(input.name)
                    "
                    @click="handleInput"
                    v-model="item[input.name]"
                    :type="getInputType(input.type)"
                    class="cms-input w-input"
                    :name="input.name"
                  />

                  <label
                    v-if="
                      input.name !== 'index' &&
                      getInputType(input.type) === 'checkbox'
                    "
                    class="w-checkbox checkbox-wrapper"
                    @click="handleInput"
                  >
                    <input
                      type="checkbox"
                      v-model="item[input.name]"
                      :name="input.name"
                      style="opacity: 0; position: absolute; z-index: -1"
                    />
                    <span
                      class="w-checkbox-input w-checkbox-input--inputType-custom cms-input checkbox"
                    ></span>
                  </label>

                  <VueDatePicker
                    v-if="
                      input.name !== 'index' &&
                      (getInputType(input.type) === 'date' ||
                        isToFromType(input.name))
                    "
                    v-model="item[input.name]"
                    :format="'yyyy-MM-dd'"
                    locale="sv"
                    auto-apply=""
                    input-class-name="cms-input dp-custom-input w-input"
                    :name="input.name"
                    :range="isToFromType(input.name)"
                    @cleared="datePickerCleared"
                  >
                  </VueDatePicker>

                  <textarea
                    v-if="
                      input.name !== 'index' &&
                      getInputType(input.type) === 'textarea'
                    "
                    @click="handleInput"
                    v-model="item[input.name]"
                    :type="getInputType(input.type)"
                    class="cms-input message w-input"
                    :name="input.name"
                  ></textarea>

                  <div
                    v-if="
                      input.name !== 'index' &&
                      getInputType(input.type) === 'file'
                    "
                    id="w-node-_59be39db-3067-b4db-62e1-04f78c919737-d10df2f5"
                    class="filename-wrapper"
                  >
                    <input
                      v-if="
                        input.name !== 'index' &&
                        getInputType(input.type) === 'file'
                      "
                      @click="handleInput"
                      @change="handleFileInput($event, input.name, item)"
                      :id="`${input.name}-${index}`"
                      :ref="`${input.name}-${index}`"
                      class="hide1"
                      :type="getInputType(input.type)"
                      :name="`${input.name}`"
                      accept=".jpg, .jpeg, .png"
                    />

                    <label
                      @click="handleInput"
                      :for="`${input.name}-${index}`"
                      class="text-s linkstyle"
                    >
                      {{ displayFilename(item[input.name]) }}
                    </label>

                    <xmark
                      @click.stop="
                        removeFile(index, `${input.name}-${index}`, input.name)
                      "
                      style="color: white"
                      class="remove-image"
                    />
                  </div>
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

    <div v-if="savingItemFlag || savingAllItemsFlag" class="saveblocker"></div>
  </div>
</template>

<script>
import { Vue3Lottie } from "vue3-lottie";
import loaderAnim from "../documents/77076-loading.json";
import { Drag, DropList } from "vue-easy-dnd";
import "vue-easy-dnd/dist/dnd.css";
import VueDatePicker from "@vuepic/vue-datepicker";
import "@vuepic/vue-datepicker/dist/main.css";

import chevronDown from "../images/chevron-down.vue";
import gripVertical from "../images/grip-vertical.vue";
import house from "../images/house.vue";
import gear from "../images/gear.vue";
import xmark from "../images/xmark.vue";

export default {
  name: "Cms",
  components: {
    Vue3Lottie,
    Drag,
    DropList,
    VueDatePicker,
    chevronDown,
    gripVertical,
    house,
    gear,
    xmark,
  },

  data() {
    return {
      schema: [],
      items: [],
      localItems: [],
      login: {},
      cmsGetSchema: `${import.meta.env.VITE_APP_CMS_URL}/schema`,
      cmsUpdateItem: `${import.meta.env.VITE_APP_CMS_URL}/update`,
      cmsUpdateItems: `${import.meta.env.VITE_APP_CMS_URL}/update-batch`,
      cmsAddItem: `${import.meta.env.VITE_APP_CMS_URL}/add`,
      cmsDeleteItem: `${import.meta.env.VITE_APP_CMS_URL}/delete`,
      cmsName: "{{ simple }} CMS",
      baserowClientToken: "ut84FNQWZzasN9CHp3Wgg9DX9ymhZcje",
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
      selectDate: new Date(),
      showDateList: false,
      sortOrder: false,
    };
  },

  async created() {
    if (this.getLocalStorage("simple-cms-login")) {
      this.login = this.getLocalStorage("simple-cms-login");
    }

    this.schema = await this.postFetch(
      this.cmsGetSchema,
      new Headers({
        "Content-Type": "application/json",
        Authorization:
          "Basic " + btoa(`${this.login.email}:${this.login.password}`),
      })
    );

    for (const schema of this.schema) {
      schema.fields = await this.getFetch(
        `https://api.baserow.io/api/database/fields/table/${schema.id}/`,
        new Headers({ Authorization: "Token " + this.baserowClientToken })
      );
    }

    this.loadData();
  },

  methods: {
    getFetch(urlEndpoint, headers, options) {
      return new Promise((resolve, reject) => {
        var requestOptions = {
          method: "GET",
          redirect: "follow",
        };

        if (headers !== null && headers !== undefined) {
          requestOptions.headers = headers;
        }

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
            reject(error);
          });
      });
    },

    postFetch(urlEndpoint, headers, body) {
      return new Promise((resolve, reject) => {
        var requestOptions = {
          method: "POST",
          redirect: "follow",
        };

        if (headers !== null && headers !== undefined) {
          requestOptions.headers = headers;
        }

        if (body !== null && body !== undefined) {
          requestOptions.body = JSON.stringify(body);
        }

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
            reject(error);
          });
      });
    },

    async loadData() {
      this.loadingFlag = true;

      this.items = await fetch(
        `https://api.baserow.io/api/database/rows/table/${
          this.schema[this.schemaIndex].id
        }/?size=200&user_field_names=true`,
        { headers: { Authorization: "Token " + this.baserowClientToken } }
      )
        .then((response) => response.json())
        .then((data) => data.results);

      this.items.sort((a, b) => {
        const indexA = a.index;
        const indexB = b.index;
        return indexA - indexB; // Ascending order
      });

      // parse to-from date-fields to json
      for (const item of this.items) {
        for (const field of Object.entries(item)) {
          if (field[0].includes("|") && field[0].includes("to-from")) {
            if (item[field[0]]) {
              item[field[0]] = JSON.parse(item[field[0]]);
            }
          }
        }
      }

      this.localItems = JSON.parse(JSON.stringify(this.items));

      this.loadingFlag = false;
      this.initLoadedFlag = true;
      this.$emit("initLoadedFlag", true);
    },

    handleClick(event, index) {
      if (
        this.saveFlag &&
        event.target.nodeName !== "INPUT" &&
        event.target.nodeName !== "TEXTAREA" &&
        event.target.nodeName !== "SPAN" &&
        event.target.nodeName !== "LABEL"
      ) {
        this.alertSaveFlag();
        return;
      }

      // if delete is clicked, do not change showItem panel
      if (event.target.innerText !== "Delete") {
        event.target.scrollIntoView({ behavior: "smooth", block: "start" });
        this.showItem = this.showItem === index ? false : index;
      }

      this.currentIndex = index;
    },

    datePickerCleared(value) {
      if (!value) {
        this.$nextTick(() => {
          this.saveFlag = true;
        }, 1000);
      }
    },

    handleClickOutside(event) {
      if (event.target.className !== "sort-by-date") {
        this.showDateList = false;
      }
    },

    alertSaveFlag() {
      const element = this.$refs["list-item-" + this.showItem].$el;

      element.scrollIntoView({
        behavior: "smooth",
        block: "start",
      });

      this.blinkAnim = true;
      setTimeout(() => {
        this.blinkAnim = false;
      }, 2000);
    },

    handleInput(event) {
      this.showItem = true;
    },

    async handleFileInput(event, name, inputFields) {
      if (!event.target.files[0].name) return;

      inputFields[name] = [
        {
          name: event.target.files[0].name,
          file: await this.readEncodeFiles(event.target.files),
        },
      ];
    },

    readEncodeFiles(files) {
      return new Promise((resolve, reject) => {
        if (files.length > 0) {
          var selectedFile = files[0];
          var reader = new FileReader();

          reader.onload = function (e) {
            var base64Data = e.target.result.split(",")[1];
            resolve(base64Data);
          };

          reader.onerror = function (error) {
            reject(error);
          };

          // Read the file as a data URL, which will be Base64-encoded
          reader.readAsDataURL(selectedFile);
        } else {
          reject(new Error("No files to process."));
        }
      });
    },

    getInputType(type) {
      let inputType = "text";

      if (type === "long_text") {
        inputType = "textarea";
      } else if (type === "date") {
        inputType = "date";
      } else if (type === "boolean") {
        inputType = "checkbox";
      } else if (type === "file") {
        inputType = "file";
      }

      return inputType;
    },

    isToFromType(inputName) {
      if (inputName.includes("|") && inputName.split("|")[1] === "to-from") {
        return true;
      }

      return false;
    },

    isItemChanged(localItems, items) {
      if ((!items && !localItems) || this.editingNewItem) return null;

      let modified = false;

      for (const [index, input] of Object.entries(localItems)) {
        const localObject = JSON.stringify(input);
        const itemsObject = JSON.stringify(items[index]);

        if (
          (localObject !== itemsObject &&
            index === this.schema[this.schemaIndex].fields[0].name &&
            input.trim() !== "") ||
          (localObject !== itemsObject &&
            input !== null &&
            index !== this.schema[this.schemaIndex].fields[0].name)
        ) {
          modified = true;
        }
      }

      return modified;
    },

    getItemJson(index) {
      let itemJson = {};
      itemJson = JSON.parse(JSON.stringify(this.localItems[index]));

      // delete keys where values are empty
      for (const item of Object.entries(itemJson)) {
        if (!item[1]) {
          delete itemJson[item[0]];
        }
      }

      itemJson.id = this.localItems[index].id;
      itemJson.tableid = this.schema[this.schemaIndex].id;

      return itemJson;
    },

    getItemOrder(index) {
      let itemJson = {};
      itemJson = {};
      itemJson.index = index;
      itemJson.id = this.localItems[index].id;

      return itemJson;
    },

    async saveItem(index, item) {
      this.savingItemFlag = true;
      this.saveFlag = false;
      const saveData = JSON.parse(JSON.stringify(item));

      // convert date-format if needed and stringify multidate-fields
      for (const field of this.schema[this.schemaIndex].fields) {
        if (field.type === "date") {
          saveData[field.name] = this.convertDateToIso(saveData[field.name]);
        }

        if (field.name.includes("|") && field.name.includes("to-from")) {
          saveData[field.name] = JSON.stringify(saveData[field.name]);
        }
      }

      if (this.editingNewItem) {
        const savedItem = await this.postFetch(
          this.cmsAddItem,
          new Headers({
            "Content-Type": "application/json",
            Authorization:
              "Basic " + btoa(`${this.login.email}:${this.login.password}`),
          }),
          {
            item: saveData,
            tableid: this.schema[this.schemaIndex].id,
            fields: this.schema[this.schemaIndex].fields,
          }
        );

        this.editingNewItem = false;
        this.localItems[index] = savedItem;
        this.localItems[index].id = savedItem.id;
      } else {
        const updateItem = await this.postFetch(
          this.cmsUpdateItem,
          new Headers({
            "Content-Type": "application/json",
            Authorization:
              "Basic " + btoa(`${this.login.email}:${this.login.password}`),
          }),
          {
            item: saveData,
            tableid: this.schema[this.schemaIndex].id,
            fields: this.schema[this.schemaIndex].fields,
          }
        );
      }

      this.items = JSON.parse(JSON.stringify(this.localItems));
      this.savingItemFlag = false;
      this.saveFlag = false;
      this.showItem = false;
    },

    convertDateToIso(date) {
      if (!date) return null;

      const originalDate = date;
      const isoDatePattern = /^\d{4}-\d{2}-\d{2}$/;

      if (isoDatePattern.test(originalDate)) {
        return originalDate;
      } else {
        // Convert the non-ISO date to ISO format and extract only the date part
        const isoDate = new Date(originalDate).toISOString().split("T")[0];
        return isoDate;
      }
    },

    async saveAllItems() {
      const itemArray = [];
      this.savingAllItemsFlag = true;
      this.saveFlag = true;

      for (const [index, item] of Object.entries(this.localItems)) {
        item.index = index;
        itemArray.push(this.getItemOrder(index));
      }

      const updateItems = await this.postFetch(
        this.cmsUpdateItems,
        new Headers({
          "Content-Type": "application/json",
          Authorization:
            "Basic " + btoa(`${this.login.email}:${this.login.password}`),
        }),
        {
          items: itemArray,
          tableid: this.schema[this.schemaIndex].id,
          fields: this.schema[this.schemaIndex].fields,
        }
      );

      this.items = JSON.parse(JSON.stringify(this.localItems));
      this.savingAllItemsFlag = false;
      this.saveFlag = false;
    },

    async cancelItem(index) {
      this.saveFlag = false;
      this.localItems = JSON.parse(JSON.stringify(this.items));
      this.showItem = false;
    },

    deleteItem(index) {
      this.savingItemFlag = true;
      this.saveFlag = false;
      this.currentIndex = index;

      setTimeout(async () => {
        this.showItem = false;

        const currentItem = this.getItemJson(index);
        const deletedItem = await this.postFetch(
          this.cmsDeleteItem,
          new Headers({
            "Content-Type": "application/json",
            Authorization:
              "Basic " + btoa(`${this.login.email}:${this.login.password}`),
          }),
          {
            tableid: currentItem.tableid,
            id: currentItem.id,
          }
        );

        this.localItems.splice(index, 1);

        this.items = JSON.parse(JSON.stringify(this.localItems));
        this.savingItemFlag = false;

        this.saveAllItems(); // to reindex after delete
      }, 100);
    },

    addItem() {
      if (this.saveFlag) {
        this.alertSaveFlag();
        return;
      }

      this.editingNewItem = true;
      const index = this.localItems.length;
      this.currentIndex = index;
      let fields = {};

      for (const item of this.schema[this.schemaIndex].fields) {
        if (item.type === "boolean") {
          fields[item.name] = false;
        } else if (item.type === "file") {
          fields[item.name] = [];
        } else {
          fields[item.name] = "";
        }
      }

      fields.index = index;

      this.localItems.push({
        ...fields,
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

    sortItems() {
      if (this.saveFlag) {
        this.alertSaveFlag();
        return;
      }

      this.showDateList = !this.showDateList;
    },

    getDateList() {
      const dateList = [];

      for (const item of this.schema[this.schemaIndex].fields) {
        if (item.type === "date") {
          dateList.push(item);
        }

        if (item.name.includes("|") && item.name.includes("to-from")) {
          dateList.push(item);
        }
      }

      return dateList;
    },

    sortDateField(event) {
      const sortedItems = [];
      const sortedItemNulls = [];
      let sortName = event.target.innerText;

      // assure that we have the full name if special date format
      for (const item of this.schema[this.schemaIndex].fields) {
        if (`${sortName}|to-from` === item.name) {
          sortName = item.name;
        }
      }

      for (const item of this.localItems) {
        if (item[sortName]) {
          sortedItems.push(item);
        } else {
          sortedItemNulls.push(item);
        }
      }

      if (!this.sortOrder) {
        this.sortOrder = true;

        sortedItems.sort((a, b) => {
          const dateA =
            typeof a[sortName] === "object"
              ? new Date(a[sortName][0])
              : new Date(a[sortName]);
          const dateB =
            typeof b[sortName] === "object"
              ? new Date(b[sortName][0])
              : new Date(b[sortName]);

          return dateA - dateB; // Ascending order
        });
      } else {
        this.sortOrder = false;

        sortedItems.sort((a, b) => {
          const dateA =
            typeof a[sortName] === "object"
              ? new Date(a[sortName][0])
              : new Date(a[sortName]);
          const dateB =
            typeof b[sortName] === "object"
              ? new Date(b[sortName][0])
              : new Date(b[sortName]);

          return dateB - dateA; // Descending order
        });
      }

      const allItems = sortedItems.concat(sortedItemNulls);
      const newList = [];
      let numberNulls = 0;

      // pair them based on the index and with the nulls (undated objects) always in their original place
      for (const [index, all] of Object.entries(allItems)) {
        let isNull = false;

        for (const nullItem of sortedItemNulls) {
          if (parseInt(nullItem.index) === parseInt(index)) {
            newList.push(nullItem);
            isNull = true;
            numberNulls = numberNulls + 1;
          }
        }

        if (!isNull) {
          newList.push(sortedItems[parseInt(index - numberNulls)]);
        }
      }

      this.localItems = JSON.parse(JSON.stringify(newList));
      this.showDateList = false;
      this.saveAllItems();
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

    displayFilename(filename) {
      if (filename && filename.length > 0) {
        if (filename[0].visible_name) {
          return filename[0].visible_name;
        } else if (filename[0].name) {
          return filename[0].name;
        }
      }

      return "Click here to choose an image.";
    },

    removeFile(index, inputName, fieldName) {
      if (this.localItems[index] && this.localItems[index][fieldName]) {
        this.$refs[inputName][0].value = "";
        this.localItems[index][fieldName] = [];
      }
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
      this.showItem = false;
      this.loadData();
      window.scrollTo(0, 0);
    },

    showItem() {
      if (this.showItem === false) {
        this.dragDelay = 0;
      } else {
        this.dragDelay = 86400000;
      }
    },

    selectDate(date) {
      this.selectDate = date;
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

input[type="date"]::-webkit-calendar-picker-indicator {
  filter: invert(100%) brightness(100%);
}

.saveblocker {
  display: block;
}
</style>

<style>
.dp-custom-input {
  padding-left: 2.3rem !important;
  border-radius: 0px;
}

input[type="checkbox"]:checked + span {
  background-color: #3898ec;
  background-image: url("https://d3e54v103j8qbb.cloudfront.net/static/custom-checkbox-checkmark.589d534424.svg");
  background-position: 50%;
  background-repeat: no-repeat;
  background-size: cover;
  border-color: #3898ec;
}

.datelist-content {
  scrollbar-width: thin;
  scrollbar-color: dimgray rgba(42, 43, 42, 0.95);
}
.datelist-content::-webkit-scrollbar {
  width: 10px;
}
.datelist-content::-webkit-scrollbar-track {
  background: rgba(42, 43, 42, 0.95);
}
.datelist-content::-webkit-scrollbar-thumb {
  background-color: dimgray;
  border-radius: 20px;
  border: 3px solid rgba(42, 43, 42, 0.95);
}
</style>
