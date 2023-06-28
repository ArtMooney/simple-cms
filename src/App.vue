<template>
  <div class="simple-cms-wrapper">
    <h1>{{ simple }} CMS</h1>
    <div>by FrameCore</div>
    <div class="collection-wrapper">
      <div v-for="table of schema" class="collection-text">
        {{ table.name }}
      </div>
    </div>
    <div class="text-s">Add, edit or remove content below</div>
    <div class="form-block w-form">
      <div class="cms-item-wrapper">
        <div
          v-for="(item, index) of items"
          @click="handleClick($event, index)"
          class="cms-item"
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
              v-if="showItem === index && saveFlag"
              @click="saveItem(index)"
              class="text-s control-buttons"
            >
              Save
            </div>
            <div
              v-if="showItem === index && saveFlag"
              @click="cancelItem(index)"
              class="text-s control-buttons"
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

            <template v-for="input of schema[0].fields">
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
export default {
  name: "App",

  data() {
    return {
      schema: {},
      items: {},
      userName: "FrameCore",
      userPass: "CMS-development",
      cmsGetBaseSchema: "https://api.framecore.se/webhook/get-base-schema",
      cmsGetWebhook: "https://api.framecore.se/webhook/simple-cms-get",
      cmsSetItemWebhook: "https://api.framecore.se/webhook/simple-cms-set",
      simple: "{{ simple }}",
      showItem: false,
      saveFlag: false,
      currentItemSave: null,
      storedItemSave: null,
      currentIndex: false,
      currentItems: null,
      storedItems: null,
      inputPointer: null,
    };
  },

  async created() {
    this.schema = await this.getCmsData(this.cmsGetBaseSchema);
    this.items = await this.getCmsData(this.cmsGetWebhook);
  },

  methods: {
    getCmsData(urlEndpoint) {
      return new Promise((resolve, reject) => {
        var requestOptions = {
          method: "GET",
          headers: {
            Authorization: "Basic " + btoa(this.userName + ":" + this.userPass),
          },
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
            reject(error);
          });
      });
    },

    jsonToFormData(object) {
      const formData = new FormData();
      Object.keys(object).forEach((key) => formData.append(key, object[key]));
      return formData;
    },

    handleClick(event, index) {
      if (this.saveFlag && event.target.nodeName !== "INPUT") return;

      this.showItem = this.showItem === index ? false : index;

      // console.log("HÄST", index !== this.currentIndex);

      // if (index !== this.currentIndex) {
      this.currentItemSave = event.srcElement;
      this.storedItemSave = this.currentItemSave.cloneNode(true);
      // }

      this.currentIndex = index;
    },

    handleInput(event) {
      this.showItem = true;

      console.log("HÄST");
      const currentItems = this.currentItemSave.querySelectorAll("input");
      const storedItems = this.storedItemSave.querySelectorAll("input");
      this.currentItems = currentItems;
      this.storedItems = storedItems;
      this.inputPointer = event.target;

      this.inputPointer.addEventListener("input", this.inputMonitor);
      this.inputPointer.addEventListener("blur", this.stopMonitor);
    },

    inputMonitor() {
      const currentItems = this.currentItems;
      const storedItems = this.storedItems;

      if (this.isItemValuesChanged(currentItems, storedItems)) {
        this.saveFlag = true;
      } else {
        this.saveFlag = false;
      }
    },

    stopMonitor() {
      this.inputPointer.addEventListener("input", this.inputMonitor);
      this.inputPointer.addEventListener("blur", this.stopMonitor);
    },

    isItemValuesChanged(currentItems, storedItems) {
      if (!currentItems && !storedItems) return null;

      let modified = false;

      for (const [index, currentInput] of Object.entries(currentItems)) {
        if (currentInput.value !== storedItems[index].value) modified = true;
      }

      return modified;
    },

    getItemJson(itemIndex, currentItems, storedItems) {
      if (!currentItems && !storedItems) return null;

      let itemJson = {};

      for (const [index, currentInput] of Object.entries(currentItems)) {
        itemJson[this.schema[0].fields[index].name] = currentInput.value;
      }

      itemJson.id = this.items[itemIndex].id;

      return itemJson;
    },

    async saveItem(index) {
      console.log("SAVE", index);
      console.log(this.getItemJson(index, this.currentItems, this.storedItems));

      this.saveFlag = false;
      this.currentIndex = index;
      await this.postCmsData(
        this.cmsSetItemWebhook,
        this.getItemJson(index, this.currentItems, this.storedItems)
      );
      this.schema = await this.getCmsData(this.cmsGetBaseSchema);
      this.items = await this.getCmsData(this.cmsGetWebhook);
      this.showItem = false;
    },

    async cancelItem() {
      console.log("CANCEL");
      this.schema = await this.getCmsData(this.cmsGetBaseSchema);
      this.items = await this.getCmsData(this.cmsGetWebhook);
      this.saveFlag = false;
      this.showItem = false;
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
};
</script>
