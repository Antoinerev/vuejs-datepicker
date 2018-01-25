<template>
  <div id="app">

    <h1>Datepicker Examples</h1>


    <div class="example">
      <h3>Highlighting Ranges</h3>
      <code>
        &lt;datepicker :highlighted="highlighted"&gt;&lt;/datepicker&gt;
      </code>
      <div class="settings">
        <h5>Settings</h5>
        <div class="form-group">
          <label>Format</label>
          <select v-model="nbOfDays">
            <option value="2" selected>Hier</option>
            <option value="7">7 derniers jours</option>
            <option value="30">30 derniers jours</option>
            <option value="90">90 derniers jours</option>
            <option value="365">365 derniers jours</option>
          </select>
        </div>
        <div class="form-group">
          <label>Highlight to:</label>
          <datepicker :inline="true" v-on:selected="highlightTo" :highlighted="highlighted"  :open-date="openDate"></datepicker>
        </div>
        <pre>
          <span>highlighted: {{ highlighted }}</span>
          <span>openDate: {{openDate}}</span>
        </pre>
      </div>
    </div>
  </div>
</template>

<script>
import Datepicker from '@/components/Datepicker'
import DateLanguages from '@/utils/DateLanguages'

const state = {
  date1: new Date()
}

export default {
  name: 'app',
  components: {
    Datepicker
  },
  data () {
    return {
      format: 'd MMMM yyyy',
      disabled: {},
      openDate: null,
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
      nbOfDays: 2
    }
  },
  watch: {
    nbOfDays () {
      this.highlightTo(this.highlighted.to)
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
        to: val,
        from: this.setRangeDays(val, this.nbOfDays),
        RangeInDays: this.nbOfDays
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

@import url('https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css');

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
