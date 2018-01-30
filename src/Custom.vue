<template>
  <div id="app">

    <h1>Range Datepicker</h1>


    <div>
      <pre>
        <span>highlighted: {{ highlighted }}</span>
        <span>presetRange: {{presetRange}}</span>
        <span>selected days: {{selectedDays.first.toDateString()}} >> {{selectedDays.last.toDateString()}}</span>
      </pre>
      <div class="btn btn-primary">{{dropdownTitle}}</div>
      <b-button-toolbar key-nav aria-label="Toolbar with button groups">
          <b-button-group class="mx-1">
            <b-btn>
              <b-dropdown id="ddown1" text="Plage" class="m-md-2" width="400" v-on:shown="highlightTo(selectedDays.last)">
                <b-dropdown-item>
                  <div class="example">
                    <div class="settings">
                      <div class="form-group">
                        <select v-model="presetRange">
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
                          :open-date="selectedDays.from"
                          :highlighted="highlighted"
                          :bootstrapStyling="true">
                        </datepicker>
                        To :<datepicker  v-on:selected="highlightTo"
                          :open-date="selectedDays.last"
                          :highlighted="highlighted"
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
            </b-btn>
            <b-btn class="m-md-2"  >
              <b-dropdown id="ddown2" text="Semaine"  class="m-md-2" width="400">
                <b-dropdown-item>
                  <div class="example">
                    <div class="settings">
                      <div class="form-group">
                        <datepicker  v-on:selected="highlightWeek"
                          :inline="true"
                          :highlighted="highlighted"
                          :open-date="openDate"
                          :bootstrapStyling="true"
                          :minimumView="'day'"
                          :maximumView="'day'">
                        </datepicker>
                      </div>
                    </div>
                  </div>
                </b-dropdown-item>
              </b-dropdown>
            </b-btn>
            <b-btn class="m-md-2">
              <b-dropdown id="ddown3" text="Mois"  class="m-md-2" width="400">
                <b-dropdown-item>
                  <div class="example">
                    <div class="settings">
                      <div class="form-group">
                        <datepicker v-on:selected="highlightMonth"
                          :inline="true"
                          :highlighted="highlighted"
                          :open-date="openDate"
                          :bootstrapStyling="true"
                          :minimumView="'month'"
                          :maximumView="'month'"
                          :initialView="'month'">
                        </datepicker>
                      </div>
                    </div>
                  </div>
                </b-dropdown-item>
              </b-dropdown>
            </b-btn>
            <b-btn class="m-md-2">
              <b-dropdown id="ddown4" text="Trimestre"  class="m-md-2" width="400">
                <b-dropdown-item>
                  <div class="example">
                    <div class="settings">
                      <div class="form-group">
                        <datepicker v-on:selected="highlightTrimester"
                          :inline="true"
                          :highlighted="highlighted"
                          :open-date="openDate"
                          :bootstrapStyling="true"
                          :minimumView="'month'"
                          :maximumView="'year'"
                          :initialView="'month'"
                          :selectionRange="'trimester'">
                        </datepicker>
                      </div>
                    </div>
                  </div>
                </b-dropdown-item>
              </b-dropdown>
            </b-btn>
            <b-btn class="m-md-2">
              <b-dropdown id="ddown5" text="Année"  class="m-md-2" width="400">
                <b-dropdown-item>
                  <div class="example">
                    <div class="settings">
                      <div class="form-group">
                        <datepicker v-on:selected="highlightYear"
                          :inline="true"
                          :highlighted="highlighted"
                          :open-date="openDate"
                          :bootstrapStyling="true"
                          :minimumView="'year'"
                          :maximumView="'year'">
                        </datepicker>
                      </div>
                    </div>
                  </div>
                </b-dropdown-item>
              </b-dropdown>
            </b-btn>
          </b-button-group>
      </b-button-toolbar>
    </div>
  </div>
</template>

<script>
import Datepicker from '@/components/Datepicker'
import DateLanguages from '@/utils/DateLanguages'
import DateUtils from '@/utils/DateUtils.js'
import bDropdown from 'bootstrap-vue/es/components/dropdown/dropdown'
import bButtonToolbar from 'bootstrap-vue/es/components/button-toolbar/button-toolbar'

const state = {
  date1: new Date()
}

export default {
  name: 'app',
  components: {
    Datepicker,
    bDropdown,
    bButtonToolbar
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
      selectedDays: {
        first: new Date(),
        last: new Date()
      },
      eventMsg: null,
      state: state,
      language: 'en',
      languages: DateLanguages.translations,
      vModelExample: null,
      presetRange: 2,
      dayInMillisecs: 86400000
    }
  },
  watch: {
    presetRange () {
      if (this.presetRange > 0) {
        this.highlightTo(new Date())
      } else {
        this.highlightTo(this.selectedDays.last)
      }
    }
  },
  computed: {
    isCustomRange () {
      return this.presetRange === '0'
    },
    dropdownTitle () {
      if (this.highlighted.from === undefined) {
        return 'Choisissez une plage de temps'
      } else {
        return DateUtils.formatDate(this.selectedDays.first, 'dd MMM yyyy') + ' - ' + DateUtils.formatDate(this.selectedDays.last, 'dd MMM yyyy')
      }
    }
  },
  methods: {
    highlightYear (val) {
      var year = val.getFullYear()
      this.selectedDays = {
        first: new Date(year, 0, 1),
        last: new Date(year + 1, 0, 0)
      }
      this.highlighted = {
        from: this.selectedDays.first,
        to: new Date(year + 1, 0, 1)
      }
    },
    highlightMonth (val) {
      var year = val.getFullYear()
      var month = val.getMonth()
      this.selectedDays = {
        first: new Date(year, month, 1),
        last: new Date(year, month + 1, 0)
      }
      this.highlighted = {
        from: this.selectedDays.first,
        to: new Date(year, month + 1, 1)
      }
    },
    highlightTrimester (val) {
      var year = val.getFullYear()
      var month = val.getMonth()
      var selectedMonthFirst = 0
      if (month > 5) {
        if (month > 8) {
          selectedMonthFirst = 9
        } else {
          selectedMonthFirst = 6
        }
      } else {
        if (month > 2) {
          selectedMonthFirst = 3
        }
      }

      this.selectedDays = {
        first: new Date(year, selectedMonthFirst, 1),
        last: new Date(year, selectedMonthFirst + 3, 0)
      }
      this.highlighted = {
        from: this.selectedDays.first,
        to: new Date(year, selectedMonthFirst + 3, 1)
      }
    },
    highlightWeek (val) {
      var dayOfWeek = val.getDay()
      if (dayOfWeek === 0) {
        this.highlighted = {
          from: new Date(Date.parse(val) - 6 * this.dayInMillisecs),
          to: val
        }
      } else {
        this.highlighted = {
          from: new Date(Date.parse(val) - (dayOfWeek - 1) * this.dayInMillisecs),
          to: new Date(Date.parse(val) + (7 - dayOfWeek) * this.dayInMillisecs)
        }
      }
      this.selectedDays = {
        first: this.highlighted.from,
        last: this.highlighted.to
      }
    },
    setRangeDays (date) {
      var dateCode = Date.parse(date)
      this.highlighted = {
        from: new Date(dateCode - ((this.presetRange - 1) * this.dayInMillisecs)),
        to: date
      }
      this.selectedDays = {
        first: new Date(dateCode - ((this.presetRange - 1) * this.dayInMillisecs)),
        last: date
      }
    },
    highlightTo (val) {
      if (typeof this.highlighted.to === 'undefined') {
        this.highlighted = {
          to: null,
          daysOfMonth: this.highlighted.daysOfMonth,
          from: this.highlighted.from
        }
      }
      if (this.presetRange > 0) {
        this.setRangeDays(val)
      } else {
        this.highlighted.to = val
        this.selectedDays.last = val
      }
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
      this.selectedDays.first = val
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
