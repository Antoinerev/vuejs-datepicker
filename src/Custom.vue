<template>
  <div id="app">

    <h1>Range Datepicker</h1>


    <div>
      <pre>
        <span>highlighted: {{ highlighted }}</span>
        <span>openDate: {{openDate}}</span>
      </pre>
      <b-dropdown id="ddown1" v-bind:text="dropdownTitle" class="m-md-2" width="400">
        <b-dropdown-item>
          <div class="example">
            <div class="settings">
              <div class="form-group">
                <label>Plage</label>
                <select v-model="nbOfDays">
                  <option value="2" selected>Hier</option>
                  <option value="7">7 derniers jours</option>
                  <option value="30">30 derniers jours</option>
                  <option value="90">90 derniers jours</option>
                  <option value="365">365 derniers jours</option>
                  <option disabled>──────────</option>
                  <option value="0">Période personnalisée</option>
                </select>
              </div>
              <span v-show="isCustomRange">
                From :<datepicker  v-on:selected="highlightFrom"
                  :bootstrapStyling="true">
                </datepicker>
                To :<datepicker  v-on:selected="highlightTo"
                  :bootstrapStyling="true">
                </datepicker>
              </span>
              <div class="form-group">
                <hr>

                <datepicker  v-on:selected="highlightTo"
                  :inline="true"
                  :highlighted="highlighted"
                  :open-date="openDate"
                  :bootstrapStyling="true">
                </datepicker>
              </div>
            </div>
          </div>
        </b-dropdown-item>
      </b-dropdown>
    </div>
  </div>
</template>

<script>
import Datepicker from '@/components/Datepicker'
import DateLanguages from '@/utils/DateLanguages'
import bDropdown from 'bootstrap-vue/es/components/dropdown/dropdown'
import bModal from 'bootstrap-vue/es/components/modal/modal'
// import BootstrapVue from 'bootstrap-vue'
// import DateUtils from '@/utils/DateUtils.js'
// Vue.use(Modal)

const state = {
  date1: new Date()
}

export default {
  name: 'app',
  components: {
    Datepicker,
    'b-modal': bModal,
    'b-dropdown': bDropdown
  },
  data () {
    return {
      format: 'd MMMM yyyy', // 'dd/MM/yyyy',
      disabled: {},
      openDate: new Date(),
      disabledFn: {
        customPredictor (date) {
          if (date.getDate() % 3 === 0) {
            return true
          }
        }
      },
      highlightedFn: {
        customPredictor (date) {
          if (date.getDate() % 4 === 0) {
            return true
          }
        }
      },
      highlighted: {
        to: new Date()
      },
      eventMsg: null,
      state: state,
      language: 'en',
      languages: DateLanguages.translations,
      vModelExample: null,
      nbOfDays: 2,
      dropdownTitle: 'Choisissez une plage de temps'
    }
  },
  watch: {
    nbOfDays () {
      this.highlightTo(this.highlighted.to)
    }
  },
  computed: {
    isCustomRange () {
      return this.nbOfDays === '0'
    }
  },
  methods: {
    setWeekRange (date) {
      const dayInMillisecs = 86400000
      var dateCode = Date.parse(date)
      var rangeStartDate = new Date(dateCode - (6 * dayInMillisecs))
      return rangeStartDate
    },
    setRangeDays (date, nbOfDays) {
      const dayInMillisecs = 86400000
      var dateCode = Date.parse(date)
      var rangeStartDate = new Date(dateCode - ((nbOfDays - 1) * dayInMillisecs))
      return rangeStartDate
    },
    highlightTo (val) {
      if (typeof this.highlighted.to === 'undefined') {
        this.highlighted = {
          to: null,
          daysOfMonth: this.highlighted.daysOfMonth,
          from: this.highlighted.from,
          RangeInDays: this.nbOfDays
        }
      }
      this.highlighted = {
        // to: DateUtils.formatDate(val, this.format),
        // from: DateUtils.formatDate(this.setRangeDays(val, this.nbOfDays), this.format),
        RangeInDays: this.nbOfDays,
        to: val,
        from: this.nbOfDays > 0 ? this.setRangeDays(val, this.nbOfDays) : this.highlighted.from
      }
      console.log('highlightTo')
    },
    highlightFrom (val) {
      if (typeof this.highlighted.from === 'undefined') {
        this.highlighted = {
          to: this.highlighted.to,
          daysOfMonth: this.highlighted.daysOfMonth,
          from: null
        }
      }
      this.highlighted.from = val
      console.log(val)
    },
    setHighlightedDays (elem) {
      if (elem.target.value === 'undefined') {
        return
      }
      let highlightedDays = elem.target.value.split(',').map(day => parseInt(day))
      this.highlighted = {
        from: this.highlighted.from,
        to: this.highlighted.to,
        daysOfMonth: highlightedDays
      }
    },
    setDisabledDays (elem) {
      if (elem.target.value === 'undefined') {
        return
      }
      let disabledDays = elem.target.value.split(',').map(day => parseInt(day))
      this.disabled = {
        from: this.disabled.from,
        to: this.disabled.to,
        daysOfMonth: disabledDays
      }
    },
    disableTo (val) {
      if (typeof this.disabled.to === 'undefined') {
        this.disabled = {
          to: null,
          daysOfMonth: this.disabled.daysOfMonth,
          from: this.disabled.from
        }
      }
      this.disabled.to = val
    },
    disableFrom (val) {
      if (typeof this.disabled.from === 'undefined') {
        this.disabled = {
          to: this.disabled.to,
          daysOfMonth: this.disabled.daysOfMonth,
          from: null
        }
      }
      this.disabled.from = val
    }
  }
}
</script>

<style>

@import url('https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css');

body {
    font-family: 'Helvetica Neue Light', Helvetica, sans-serif;
    padding: 1em 2em 2em;
}
input, select {
    padding: .75em .5em;
    font-size: 100%;
    border: 1px solid #ccc;
    width: 100%
}

select {
    height: 2.5em;
}

.example {
    background: #f2f2f2;
    border: 1px solid #ddd;
    padding: 0em 1em 1em;
    margin-bottom: 2em;
}

code,
pre {
    margin: 1em 0;
    padding: 1em;
    border: 1px solid #bbb;
    display: block;
    background: #ddd;
    border-radius: 3px;
}

.settings {
    margin: 2em 0;
    border-top : 1px solid #bbb;
    background: #eee;
}

h5 {
    font-size:100%;
    padding: 0;
}

.form-group {
    margin-bottom: 1em;
}

.form-group label {
    font-size: 80%;
    display: block;
}
</style>
