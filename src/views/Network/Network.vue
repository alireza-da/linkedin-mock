<template>
  <div class="fill-height">
    <v-container v-if="!loading" fluid>
      <v-row justify="center">
        <v-col cols="4">
          <user-info-card />
        </v-col>
        <v-col cols="8">
          <v-row>
            <v-col cols="12">
              <invitations-card @respond="updateConnections" @error="(alert) => reqStatus = alert"/>
            </v-col>
            <v-col cols="12">
              <suggestions/>
            </v-col>
          </v-row>
        </v-col>

      </v-row>
    </v-container>
    <spinner v-else/>

    <custom-alert v-model="reqStatus.status" @input="reqStatus.status = !reqStatus.status" :message="reqStatus.message"
                  :type="reqStatus.type"/>
  </div>
</template>

<script>
  import Spinner from "../../components/Loaders/Spinner";
  import InvitationsCard from "../../components/Network/InvitationsCard";
  import Suggestions from "../../components/Network/Suggestions";
  import {mapActions, mapGetters} from "vuex";
  import CustomAlert from "../../components/Alerts/CustomAlert";
  import UserInfoCard from "../../components/Cards/UserInfoCard";

  export default {
    name: "Network",
    components: {UserInfoCard, CustomAlert, Suggestions, InvitationsCard, Spinner},
    data() {
      return {
        loading: false,

        reqStatus: {
          message: "",
          type: "",
          status: false
        },
      }
    },

    methods: {
      ...mapActions({
        getPendingRequestsReceived: "networkModule/getPendingRequestsReceived",
        setTypeItems: "typeModule/setTypeItems",
        getNetworkSuggestions: "networkModule/getNetworkSuggestions",
        getConnectionsSent: "networkModule/getConnectionsSent",
        getConnectionsReceived: "networkModule/getConnectionsReceived"
      }),

      async initTypes() {
        await this.setTypeItems("connectTypes");
      },

      async updateConnections(alert) {
        this.reqStatus = alert;
        await this.getPendingRequestsReceived();
      }
    },

    async created() {
      this.loading = true;
      await this.getPendingRequestsReceived();
      await this.initTypes();
      await this.getConnectionsSent();
      await this.getConnectionsReceived();
      await this.getNetworkSuggestions();
      this.loading = false;
    }
  }
</script>

<style scoped>

</style>
