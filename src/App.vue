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
              class="text-s save-button"
            >
              Save
            </div>
            <div
              id="w-node-_9b7e1cbf-3ced-61f8-24e8-1f5c039f38d8-d10df2f5"
              :class="[
                showItem === index
                  ? 'arrow-down-white rotated w-embed'
                  : 'arrow-down-white w-embed',
              ]"
            >
              <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 448 512">
                <!--! Font Awesome Free 6.4.0 by @fontawesome - https://fontawesome.com License - https://fontawesome.com/license/free (Icons: CC BY 4.0, Fonts: SIL OFL 1.1, Code: MIT License) Copyright 2023 Fonticons, Inc. -->
                <path
                  fill="CurrentColor"
                  d="M201.4 342.6c12.5 12.5 32.8 12.5 45.3 0l160-160c12.5-12.5 12.5-32.8 0-45.3s-32.8-12.5-45.3 0L224 274.7 86.6 137.4c-12.5-12.5-32.8-12.5-45.3 0s-12.5 32.8 0 45.3l160 160z"
                />
              </svg>
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
    <div class="cms-close-button w-embed">
      <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 384 512">
        <!--! Font Awesome Free 6.4.0 by @fontawesome - https://fontawesome.com License - https://fontawesome.com/license/free (Icons: CC BY 4.0, Fonts: SIL OFL 1.1, Code: MIT License) Copyright 2023 Fonticons, Inc. -->
        <path
          fill="CurrentColor"
          d="M342.6 150.6c12.5-12.5 12.5-32.8 0-45.3s-32.8-12.5-45.3 0L192 210.7 86.6 105.4c-12.5-12.5-32.8-12.5-45.3 0s-12.5 32.8 0 45.3L146.7 256 41.4 361.4c-12.5 12.5-12.5 32.8 0 45.3s32.8 12.5 45.3 0L192 301.3 297.4 406.6c12.5 12.5 32.8 12.5 45.3 0s12.5-32.8 0-45.3L237.3 256 342.6 150.6z"
        />
      </svg>
    </div>
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

      if (index !== this.currentIndex) {
        this.currentItemSave = event.srcElement;
        this.storedItemSave = this.currentItemSave.cloneNode(true);
      }

      this.currentIndex = index;
    },

    handleInput(event) {
      this.showItem = true;

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
      await this.postCmsData(
        this.cmsSetItemWebhook,
        this.getItemJson(index, this.currentItems, this.storedItems)
      );
      this.schema = await this.getCmsData(this.cmsGetBaseSchema);
      this.items = await this.getCmsData(this.cmsGetWebhook);
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
