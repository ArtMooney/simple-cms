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
          @click="handleClick(index)"
          class="cms-item"
        >
          <div id="w-node-ec545091-3119-9423-b023-febb8072a9c9-d10df2f5">
            {{ item.titel }}
          </div>
          <div
            id="w-node-_9b7e1cbf-3ced-61f8-24e8-1f5c039f38d8-d10df2f5"
            class="arrow-down-white w-embed"
          >
            <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 448 512">
              <!--! Font Awesome Free 6.4.0 by @fontawesome - https://fontawesome.com License - https://fontawesome.com/license/free (Icons: CC BY 4.0, Fonts: SIL OFL 1.1, Code: MIT License) Copyright 2023 Fonticons, Inc. -->
              <path
                fill="CurrentColor"
                d="M201.4 342.6c12.5 12.5 32.8 12.5 45.3 0l160-160c12.5-12.5 12.5-32.8 0-45.3s-32.8-12.5-45.3 0L224 274.7 86.6 137.4c-12.5-12.5-32.8-12.5-45.3 0s-12.5 32.8 0 45.3l160 160z"
              />
            </svg>
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
                v-model="item.titel"
                type="email"
                class="cms-input w-input"
                maxlength="256"
                name="email-2"
                data-name="Email 2"
                placeholder=""
                id="email-2"
                required=""
              />
            </template>

            <!-- <div class="text-s">Datum:</div>
            <input
              type="email"
              class="cms-input w-input"
              maxlength="256"
              name="email"
              data-name="Email"
              placeholder=""
              id="email"
              required=""
            />
            <div class="text-s">Lokal:</div>
            <input
              type="email"
              class="cms-input w-input"
              maxlength="256"
              name="email-2"
              data-name="Email 2"
              placeholder=""
              id="email-2"
              required=""
            />
            <div class="text-s">Org:</div>
            <input
              type="email"
              class="cms-input w-input"
              maxlength="256"
              name="email-2"
              data-name="Email 2"
              placeholder=""
              id="email-2"
              required=""
            />
            <div class="text-s">Tid:<br /></div>
            <input
              type="email"
              class="cms-input w-input"
              maxlength="256"
              name="email-2"
              data-name="Email 2"
              placeholder=""
              id="email-2"
              required=""
            />
            <div class="text-s">Övrig info:<br /></div>
            <textarea
              placeholder=""
              maxlength="5000"
              id="field"
              name="field"
              data-name="Field"
              class="cms-input message w-node-_8699eab2-e2be-b7dc-8ee4-34fc01ef4bd0-d10df2f5 w-input"
            ></textarea> -->
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
      simple: "{{ simple }}",
      showItem: false,
    };
  },

  async created() {
    this.schema = await this.getCmsData(this.cmsGetBaseSchema);
    this.items = await this.getCmsData(this.cmsGetWebhook);
  },

  methods: {
    handleClick(index) {
      if (this.showItem === index) {
        this.showItem = false;
      } else {
        this.showItem = index;
      }
    },

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

    getCookie(name) {
      var value = "; " + document.cookie;
      var parts = value.split("; " + name + "=");
      if (parts.length === 2) {
        return parts.pop().split(";").shift();
      }
    },
  },
};
</script>
