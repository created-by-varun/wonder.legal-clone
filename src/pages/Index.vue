<template>
  <q-page class="flex">
    <div class="col left-side q-pa-md">
      <q-scroll-area class="fit">
        <q-input
          outlined
          v-model="name"
          label="Enter the name of the recipient"
        />
        <br />

        <q-input outlined v-model="position" label="Enter the position" />
        <br />

        <q-input
          outlined
          v-model="companyName"
          label="Name of the organization"
        />
        <br />

        <q-input outlined v-model="startDate" label="The contract begins on" />
        <br />

        <q-input outlined v-model="months" label="Duration in months" />
        <br />

        <q-input outlined v-model="endDate" label="Ending date" /> <br />
      </q-scroll-area>
    </div>
    <div class="col right-side q-pa-md">
      <div class="text-center text-h5 text-weight-regular">
        Document Preview
      </div>
      <q-separator />
      <br />
      <br />

      <div class="document">
        <div class="text-h5 text-center text-weight-light">OFFER LETTER</div>
        <br />
        <br />
        <p>
          Dear <b>{{ name }}</b> <span v-if="name.length < 1">______</span> ,
          <br />

          We are delighted to offer you the position of <b>{{ position }}</b>
          <span v-if="position.length < 1">________</span> at our company,
          <b>{{ companyName }}</b>
          <span v-if="companyName.length < 1">________</span> starting from
          <b>{{ startDate }}</b>
          <span v-if="startDate.length < 1">________</span> for a period of
          <b>{{ months }}</b>
          <span v-if="months.length < 1">________</span> months until
          <b>{{ endDate }}</b> <span v-if="endDate.length < 1">________</span>.
        </p>
      </div>

      <br />
      <q-btn
        v-if="
          name.length > 0 &&
          position.length > 0 &&
          companyName.length > 0 &&
          startDate.length > 0 &&
          months.length > 0 &&
          endDate.length > 0
        "
        color="primary"
        @click="createPDF"
        icon="get_app"
        label="Download PDF"
      />

      <q-btn
        v-else
        color="primary"
        disabled
        icon="get_app"
        label="Download PDF"
      >
        <q-tooltip> Please fill all fields to download PDF! </q-tooltip>
      </q-btn>
    </div>
    <!-- end col -->
  </q-page>
</template>

<script>
import { jsPDF } from "jspdf";
import { QSpinnerFacebook } from "quasar";

export default {
  name: "PageIndex",
  data() {
    return {
      name: "",
      position: "",
      startDate: "",
      months: "",
      endDate: "",
      companyName: "",
    };
  },
  methods: {
    createPDF() {
      let pdfName = "offer-letter";
      var lMargin = 15; //left margin in mm
      var rMargin = 15; //right margin in mm
      var pdfInMM = 210; // width of A4 in mm
      var pageCenter = pdfInMM / 2;
      var pageleft = 20;

      var doc = new jsPDF("p", "mm", "a4");
      var paragraph = `Dear ${this.name},\nWe are delighted to offer you the position of ${this.position} at our company, ${this.companyName} starting from ${this.startDate} for a period of ${this.months} until ${this.endDate}`;

      var lines = doc.splitTextToSize(paragraph, pdfInMM - lMargin - rMargin);
      doc.setLineHeightFactor(1.5);
      doc.text("OFFER LETTER", pageCenter, 20, "center");
      doc.text(lines, pageleft, 40, "left"); //see this line
      doc.save(pdfName + ".pdf");
      this.$q.notify({
        message: "Downloaded PDF!",
        color: "secondary",
      });
    },
    showLoading() {
      const spinner =
        typeof QSpinnerFacebook !== "undefined"
          ? QSpinnerFacebook
          : Quasar.components.QSpinnerFacebook;
      this.$q.loading.show({
        spinner,
        spinnerColor: "blue",
        spinnerSize: 140,
        backgroundColor: "white",
        message: "<b>Please wait while the data is loading...</b>",
        messageColor: "black",
      });
      this.timer = setTimeout(() => {
        this.$q.loading.hide();
        this.timer = void 0;
      }, 3500);
    },
    beforeDestroy() {
      if (this.timer !== void 0) {
        clearTimeout(this.timer);
        this.$q.loading.hide();
      }
    },
  },

  beforeMount() {
    this.showLoading();
  },
};
</script>

<style lang="scss">
.left-side {
  background-color: $cyan-2;
}
p {
  font-size: 18px;
}
</style>
