<template>
  <div class="container px-4">
    <h1 class="has-text-centered has-text-weight-bold mb-3 is-size-2">
      How Many Xbox?
    </h1>
    <p class="mb-3 is-italic has-text-centered">
      for when you need to know how many {{ xboxPlural }} you could buy with a
      bunch of money
    </p>
    <hr />
    <form class="mb-3">
      <label for="damage">How much damage was done?</label>
      <span class="symbol">$</span>
      <input type="number" id="damage" v-model="damage" />

      <label for="year">In what year did this occur?</label>
      <span></span>
      <input type="number" id="year" v-model="year" />

      <label for="year"> How much does an Xbox cost in 2021? </label>
      <span class="symbol">$</span>
      <input type="number" id="year" v-model="xboxCost" />
    </form>
    <hr />
    <div class="results mb-6 has-text-centered">
      <p class="mb-3">
        ${{ commaize(damage) }} in {{ clampYear(year) }} is equivalent to
        <strong>${{ commaize(adjDamage) }}</strong> in {{ currentYear }}.
      </p>
      <p class="mb-3">
        You could buy <strong>{{ commaize(count) }}</strong>
        {{ xboxPlural }} with that much money.
      </p>
    </div>
    <div class="credits py-3 is-size-6 has-text-centered">
      made by
      <a href="https://twitter.com/mplewis" target="_blank">Matt Lewis</a> |
      support
      <a href="https://www.patreon.com/wtyppod" target="_blank">
        WTYP on Patreon
      </a>
      | source on
      <a href="https://github.com/mplewis/how-many-xbox" target="_blank">
        GitHub
      </a>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import dv from "./dollar_value.json";
import "bulma/bulma.sass";

const dollarValue: { [y: string]: number } = dv;

const CURRENT_YEAR = 2021;
const YEAR_BOUNDS = [1800, 2021];
const XBOX_PLURALS = ["Xbox", "Xboxes", "Xboxen", "Xboxi"];

function clampYear(year: number): number {
  if (year < YEAR_BOUNDS[0]) {
    return YEAR_BOUNDS[0];
  }
  if (year > YEAR_BOUNDS[1]) {
    return YEAR_BOUNDS[1];
  }
  return year;
}

function valueRatio(from: number, to: number): number {
  const f = dollarValue[clampYear(from).toString()];
  const t = dollarValue[clampYear(to).toString()];
  return t / f;
}

function commaize(n: number): string {
  const chars = n.toString().split("").reverse();
  let i = 0;
  let s: string[] = [];
  chars.forEach((c) => {
    i += 1;
    if (i % 3 == 1 && i > 1) s.push(",");
    s.push(c);
  });
  return s.reverse().join("");
}

export default defineComponent({
  name: "App",
  data: () => ({
    currentYear: CURRENT_YEAR,
    damage: 4200069,
    year: 1975,
    xboxCost: 299,
  }),
  methods: { commaize, clampYear },
  computed: {
    adjDamage(): number {
      return Math.round(this.damage * valueRatio(this.year, CURRENT_YEAR));
    },
    count(): number {
      return Math.round(this.adjDamage / this.xboxCost);
    },
    xboxPlural(): string {
      return XBOX_PLURALS[Math.floor(Math.random() * XBOX_PLURALS.length)];
    },
  },
});
</script>

<style lang="scss">
@font-face {
  font-family: "Go Mono";
  font-weight: 500;
  src: url("/public/Go-Mono.ttf");
}
@font-face {
  font-family: "Go Mono";
  font-weight: 700;
  src: url("/public/Go-Mono-Bold.ttf");
}

#app {
  max-width: 800px;
  display: block;
  margin: 40px auto;
  font-family: "Go Mono";
  color: #ccc;
}

html {
  background-color: #1d291d;
}

a {
  color: #2fc42f;
  &:hover {
    color: #85ff85;
  }
}

h1,
strong {
  color: #eee;
}

hr {
  background-color: #315e31;
}

form {
  display: grid;
  grid-template-columns: 2fr 30px 1fr;
  grid-column-gap: 0.25rem;
  grid-row-gap: 0.75rem;

  @media screen and (max-width: 600px) {
    grid-template-columns: 2fr 30px 120px;
  }

  label,
  .symbol {
    text-align: right;
  }
}

.credits {
  opacity: 0.5;
  &:hover {
    opacity: 1;
  }
}
</style>
