<template>
  <div class="container">
    <div class="header">
        <a href="https://lamoille.tilsontech.com/" class="logo">
        <img src="~assets/images/LCPC_survey_logo.jpg" width="250" height="59"/>
        </a>
        <div class="header-right">
        </div>
      </div>
    <div class="form-container" id="speedcheckcontainer">
      <div class="introtitle">
        <h1>Lamoille County Internet Survey</h1>
      </div>
    <br>
    <br>
      <button class="no-internet" @click="initializeForm">RUN SURVEY</button>
    </div>
    <div id="survey-panel" class="panel panel-white panel-no-border hide">
      <div id="survey123-webform" class="panel panel-no-padding panel-no-border"></div>        
    </div> 
  </div>
</template>

<script>
export default {
  mounted: function() {
    let vm = this;
    var open = window.XMLHttpRequest.prototype.open,
      send = window.XMLHttpRequest.prototype.send;

    function openReplacement(method, url, async, user, password) {
      this._url = url;
      return open.apply(this, arguments);
    }

    function sendReplacement(data) {
      /**
       * PLACE HERE YOUR CODE WHEN REQUEST IS SENT
       */
      if (this._url == "https://api.speedspot.org/basic" && arguments[0]) {
        console.log(arguments[0]);
        vm.results = JSON.parse(arguments[0]);
        if (!vm.formInitialized) {
          vm.initializeForm(vm.results);
        }
      }
      return send.apply(this, arguments);
    }

    window.XMLHttpRequest.prototype.open = openReplacement;
    window.XMLHttpRequest.prototype.send = sendReplacement;
  },
  data: function() {
    return {
      results: {},
      formInitialized: false
    };
  },
  methods: {
    initializeForm: function(speedData) {
      this.formInitialized = true;
      document.getElementById("speedcheckcontainer").style.display = "none";
      const survey123WebForm = new Survey123WebForm({
        container: "survey123-webform",
        clientId: "Jy4JtM71ralXVggd",
        portalUrl: "https://www.arcgis.com",
        itemId: "d65556a6d24945dbb3a11999f6ef9ff9",
        hideElements: "navbar",
        onFormSubmitted: function scrollWin() {
          window.scrollTo(0,0);
        },
      });
      // SHOW SURVEY123 FORM //
      let surveyPanel = document.getElementById("survey-panel");
      surveyPanel.classList.remove("hide");
    }
  }
};
</script>

<style lang="scss">
#speedcheckcontainer {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  min-height: calc(75vh - 60px);
  .introtitle {
    max-width: 900px;
    text-align: center;
    margin: 15x;
    color: #2aaee1;
  }
}
.no-internet {
  background: #2aaee1;
  color: #fff;
  font-family: verdana;
  word-spacing: 1;
  border-radius: 25px;
  display: block;
  width: 300px;
  font-size: 16px;
  padding: 10px;
  text-align: center;
  margin: 20px auto;
  cursor: pointer;
}
.no-internet:hover {
  background-color: rgb(144, 180, 221);
  color: #fff;
}
.iframecontainer {
  margin: 40px;
  background-color: rgb(26, 26, 26);
}
.form-container {
  display: flex;
  justify-content: center;
  align-items: center;
  #sc-container {
    .sc-bb {
      display: none;
    }
    .sc-button {
      cursor: pointer;
    }
    #sc-basic-link {
      display: none;
    }
    #sc-footer {
      display: none;
    }
  }
}
@media screen and (max-width: 900px) {
  .no-internet a {
    width: 100px;
  }
}
</style>
