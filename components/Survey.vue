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
        <h3>Please run the speed test below to begin the survey process, we look forward to capturing your feedback in an effort to improve and expand coverage in your area.</h3>
      </div>
      <div id="sc-container">
        <div id="sc-branding" class="sc-bb">
          <a target="_blank" href="https://www.speedcheck.org/">
            <img src="https://cdn.speedcheck.org/branding/speedcheck-logo-18.png" alt="Speedcheck" />
          </a>
        </div>
      </div>    
      <button class="no-internet" @click="initializeForm">I do not have Internet access at my residence</button>
    </div>
    <script src="https://cdn.speedcheck.org/basic/scbjs.min.js" async></script>
    <div id="survey-panel" class="panel panel-white panel-no-border hide">
      <div id="survey123-webform" class="panel panel-no-padding panel-no-border"></div>
    </div>
  </div>
</template>

<script>
export default {
  head: {
    title: 'Lamoille Internet Survey',
    meta: [
      {
        hid: 'description', name: 'description', content: 'Page description'
      }
    ],

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
  }
  },

  methods: {
    initializeForm: function(speedData) {
      this.formInitialized = true;
      document.getElementById("speedcheckcontainer").style.display = "none";
      const survey123WebForm = new Survey123WebForm({
        container: "survey123-webform",
        clientId: "Jy4JtM71ralXVggd",
        portalUrl: "https://www.arcgis.com",
        itemId: "f55e26a4adcd4e27bfa28b3172661451",
        hideElements: "navbar",
        onFormLoaded: data => {
          //
          // ANSWER INTERNET SPEED QUESTION WITH CONNECTION SPEED INFO //
          //
          survey123WebForm.setQuestionValue({
            field_2: speedData.downloadValue,
            field_3: speedData.uploadValue,
            latency: speedData.pingValue,
            internet_browser: speedData.browser["browser"],
            operating_sys: speedData.browser["os"],
            user_device: speedData.browser["device"],
            user_isp: speedData.isp["isp"]
          });
        }
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
  height: calc(100vh - 80px);
  .introtitle {
    max-width: 900px;
    text-align: center;
    margin: 15px;
    color: #2aaee1;
  }
}
.no-internet {
  background: #909090;
  color: #fff;
  font-family: verdana;
  word-spacing: 1;
  border-radius: 25px;
  display: block;
  width: 20%;
  font-size: 12px;
  font-weight: lighter;
  text-transform: uppercase;
  padding: 10px;
  text-align: center;
  margin: 20px auto;
  cursor: pointer;
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

@media screen and (max-width: 500px) {
  .no-internet a {
    float: none;
    display: block;
    width: 80%;
    height: 80%
  }
}
</style>
