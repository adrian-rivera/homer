<template>
  <Generic :item="item">
    <template #content>
      <p class="title is-4">{{ item.name }}</p>
      <p class="subtitle is-6">
        <template v-if="item.subtitle">
          {{ item.subtitle }}
        </template>
        <template v-else-if="versionstring">
          <span class="is-hidden-touch">Version {{ versionstring }}</span>
          <span class="is-hidden-desktop"
            >Version {{ versionstring.split("-").pop() }}</span
          >
        </template>
      </p>
    </template>
    <template #indicator>
      <div v-if="status" class="status" :class="status">
        {{ status }}
      </div>
    </template>
  </Generic>
</template>

<script>
import service from "@/mixins/service.js";

export default {
  name: "TruenasScale",
  mixins: [service],
  props: {
    item: Object,
  },
  data: () => ({
    fetchOk: null,
    versionstring: null,
  }),
  computed: {
    status: function () {
      return this.fetchOk ? "online" : "offline";
    },
  },
  created() {
    this.fetchStatus();
  },
  methods: {
    fetchStatus: async function () {
      let headers = {};
      if (this.item.api_token) {
        headers["Authorization"] = `Bearer ${this.item.api_token}`;
      }
      this.fetch("/api/v2.0/system/version", { headers })
        .then((response) => {
          this.fetchOk = true;
          this.versionstring = response;
        })
        .catch((e) => {
          this.fetchOk = false;
          console.log(e);
        });
    },
  },
};
</script>

<style scoped lang="scss">
.status {
  font-size: 0.8rem;
  color: var(--text-title);
  white-space: nowrap;
  margin-left: 0.25rem;

  &.online:before {
    background-color: #94e185;
    border-color: #78d965;
    box-shadow: 0 0 5px 1px #94e185;
  }

  &.offline:before {
    background-color: #c9404d;
    border-color: #c42c3b;
    box-shadow: 0 0 5px 1px #c9404d;
  }

  &:before {
    content: " ";
    display: inline-block;
    width: 7px;
    height: 7px;
    margin-right: 10px;
    border: 1px solid #000;
    border-radius: 7px;
  }
}
</style>
