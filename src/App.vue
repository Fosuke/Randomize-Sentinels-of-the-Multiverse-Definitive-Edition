<template>
  <v-app>
    <v-main class="bg-grey-lighten-4">
      <v-container>
        <v-dialog
          v-model="dialog"
          fullscreen
          scrollable
          :scrim="false"
          transition="dialog-bottom-transition"
        >
          <template v-slot:activator="{ props }"> </template>
          <v-card>
            <v-card-title class="pa-0 ma-0">
              <v-toolbar dark color="primary" flat>
                <v-toolbar-title>Settings</v-toolbar-title>
                <v-spacer></v-spacer>
                <v-toolbar-items>
                  <v-btn variant="text" @click="save"> Save </v-btn>
                </v-toolbar-items>
              </v-toolbar>
              <v-tabs
                v-model="settingsTab"
                bg-color="primary"
                class="text-no-wrap"
              >
                <v-tab
                  v-for="item in settingsTabItems"
                  :key="item"
                  :value="item"
                >
                  {{ item }}
                </v-tab>
              </v-tabs>
            </v-card-title>
            <v-card-text class="pa-0 ma-0">
              <v-window v-model="settingsTab">
                <v-window-item
                  :value="settingsTabItems[0]"
                  class="rounded-shaped bg-red-darken-2"
                >
                  <div
                    class="d-flex justify-center align-baseline"
                    style="gap: 1rem"
                  >
                    <v-btn
                      @click="clear(villains)"
                      size="small"
                      variant="outlined"
                      :color="clearColor"
                      >Clear Villains</v-btn
                    >
                    <v-btn
                      @click="selectAll(villains)"
                      size="small"
                      variant="outlined"
                      :color="selectColor"
                      >Select All Villains</v-btn
                    >
                  </div>
                  <div class="d-flex align-center flex-column">
                    Advanced:
                    <v-btn-toggle
                      v-model="advanced"
                      divided
                      variant="outlined"
                      size="small"
                      color="white"
                      density="compact"
                    >
                      <v-btn :value="false"
                        ><v-icon icon="mdi-minus-box"></v-icon
                      ></v-btn>
                      <v-btn value="random"
                        ><v-icon icon="mdi-help"></v-icon
                      ></v-btn>
                      <v-btn :value="true"
                        ><v-icon icon="mdi-alpha-a-box"></v-icon
                      ></v-btn>
                    </v-btn-toggle>
                  </div>
                  <div class="d-flex align-center flex-column">
                    Event:
                    <v-btn-toggle
                      v-model="event"
                      variant="outlined"
                      divided
                      size="small"
                      color="white"
                      density="compact"
                    >
                      <v-btn :value="false"
                        ><v-icon icon="mdi-minus-box"></v-icon
                      ></v-btn>
                      <v-btn value="random"
                        ><v-icon icon="mdi-help"></v-icon
                      ></v-btn>
                      <v-btn :value="true"
                        ><v-icon icon="mdi-alpha-e-box"></v-icon
                      ></v-btn>
                    </v-btn-toggle>
                  </div>
                  <div
                    v-for="(villain, index) in villains"
                    :key="villain.name"
                    :class="{ 'bg-red-lighten-2': !villain.active }"
                  >
                    <v-switch
                      v-model="villains[index].active"
                      hide-details
                      inset
                      v-show="activeSet(villain.set)"
                      :label="`${villain.name}`"
                    ></v-switch>
                  </div>
                </v-window-item>
                <v-window-item
                  :value="settingsTabItems[1]"
                  class="rounded-shaped bg-purple-darken-2"
                >
                  <div
                    class="d-flex justify-center align-baseline"
                    style="gap: 1rem"
                  >
                    <v-btn
                      @click="clear(env)"
                      size="small"
                      variant="outlined"
                      :color="clearColor"
                      >Clear Environment</v-btn
                    >
                    <v-btn
                      @click="selectAll(env)"
                      size="small"
                      variant="outlined"
                      :color="selectColor"
                      >Select All Environment</v-btn
                    >
                  </div>
                  <div
                    v-for="(envDeck, index) in env"
                    :key="envDeck.name"
                    :class="{ 'bg-purple-lighten-2': !envDeck.active }"
                  >
                    <v-switch
                      v-model="env[index].active"
                      hide-details
                      inset
                      v-show="activeSet(envDeck.set)"
                      :label="`${envDeck.name}`"
                    ></v-switch>
                  </div>
                </v-window-item>
                <v-window-item
                  :value="settingsTabItems[2]"
                  class="rounded-shaped bg-blue-darken-2"
                >
                  <div
                    class="d-flex justify-center align-baseline"
                    style="gap: 1rem"
                  >
                    <v-btn
                      @click="clear(heroes)"
                      size="small"
                      variant="outlined"
                      :color="clearColor"
                      >Clear Heroes</v-btn
                    >
                    <v-btn
                      @click="selectAll(heroes)"
                      size="small"
                      variant="outlined"
                      :color="selectColor"
                      >Select All Heroes</v-btn
                    >
                  </div>
                  <div
                    v-for="(hero, index) in heroes"
                    :key="hero.name"
                    :class="{ 'bg-blue-lighten-2': !hero.active }"
                  >
                    <v-switch
                      v-model="heroes[index].active"
                      hide-details
                      inset
                      v-show="activeSet(hero.set)"
                      :label="`${hero.name}`"
                    ></v-switch>
                    <v-fade-transition
                      v-show="heroes[index].active && activeSet(hero.set)"
                    >
                      <v-list
                        density="compact"
                        class="py-0 rounded-shaped bg-blue-grey-lighten-5"
                      >
                        <v-list-item
                          v-for="(variant, vindex) in hero.variants"
                          :key="variant.name"
                          variant="outlined"
                          :class="{ 'bg-grey-lighten-1': !variant.active }"
                          v-show="activeSet(variant.set)"
                        >
                          <v-switch
                            v-model="heroes[index].variants[vindex].active"
                            hide-details
                            inset
                            :label="`${variant.name}`"
                          ></v-switch>
                        </v-list-item>
                      </v-list>
                    </v-fade-transition>
                  </div>
                </v-window-item>
                <v-window-item
                  :value="settingsTabItems[3]"
                  class="rounded-shaped"
                >
                  <div v-for="(set, index) in sets" :key="set.name">
                    <v-switch
                      v-model="sets[index].active"
                      hide-details
                      inset
                      :label="`${set.name}`"
                    ></v-switch>
                  </div>
                </v-window-item>
                <v-window-item
                  :value="settingsTabItems[4]"
                  class="rounded-shaped"
                >
                  <v-row>
                    <v-col>
                      <v-alert
                        density="compact"
                        type="warning"
                        title="Settings Settings"
                      >
                        This is experimental feature with no guide rails. Here
                        the underlying data structure used to populate
                        everything is made editable. Exposing this allows you to
                        modify whatever you want or add your own custom
                        content.<br />
                        <b>Safety not guaranteed.</b>
                      </v-alert>
                    </v-col>
                  </v-row>
                  <v-row>
                    <v-col>
                      <v-textarea rows="15" v-model="textSettings"></v-textarea>
                    </v-col>
                  </v-row>
                  <v-row>
                    <v-col>
                      <v-btn color="secondary" @click="defaultSettings"
                        >Default Settings</v-btn
                      >
                    </v-col>
                    <v-col>
                      <v-btn color="info" @click="reloadSettings"
                        >Reload Settings</v-btn
                      >
                    </v-col>
                    <v-col>
                      <v-btn color="success" @click="loadSettings"
                        >Load Settings</v-btn
                      >
                    </v-col>
                  </v-row>
                </v-window-item>
              </v-window>
            </v-card-text>
          </v-card>
        </v-dialog>
        <v-row>
          <v-col>
            <v-card class="bg-blue-lighten-5">
              <v-card-item class="rounded bg-blue-grey-lighten-4">
                <v-row>
                  <v-col sm="11" cols="10">
                    <v-card-title :class="{ 'text-wrap': inactiveGame }"
                      >Randomize Sentinels of the Multiverse: Definitive
                      Edition</v-card-title
                    >

                    <v-card-subtitle>By Fosuke</v-card-subtitle>
                  </v-col>
                  <v-col cols="1">
                    <v-card-actions class="pa-0">
                      <v-btn dark icon="mdi-cog" @click="dialog = true">
                      </v-btn>
                    </v-card-actions>
                  </v-col>
                </v-row>
              </v-card-item>
              <div class="d-flex align-center flex-column">
                Hero Count:
                <v-btn-toggle
                  v-model="H"
                  divided
                  size="small"
                  color="deep-purple-accent-3"
                  density="compact"
                >
                  <v-btn size="small" value="3">3</v-btn>
                  <v-btn size="small" value="4">4</v-btn>
                  <v-btn size="small" value="5">5</v-btn>
                  <v-btn size="small" value="?">?</v-btn>
                </v-btn-toggle>
              </div>
              <v-card-text class="py-0">
                <v-list class="bg-blue-lighten-5">
                  <v-list-item
                    v-if="gameSetup.villain"
                    variant="outlined"
                    class="rounded-shaped bg-red-darken-2"
                  >
                    <v-row>
                      <v-col cols="3" md="2">
                        <v-btn
                          size="small"
                          @click="newVillain"
                          :color="randomButton"
                        >
                          <v-icon icon="mdi-shuffle"></v-icon>
                        </v-btn>
                      </v-col>
                      <v-col>
                        <v-list-item-title class="text-wrap">
                          {{ gameSetup.villain.name }}
                          <span v-show="gameSetup.advanced"
                            ><v-icon icon="mdi-alpha-a-box"
                          /></span>
                          <span v-show="gameSetup.event"
                            ><v-icon icon="mdi-alpha-e-box"
                          /></span>
                        </v-list-item-title>
                        <div
                          v-show="
                            gameSetup.villain.name != gameSetup.villain.deck
                          "
                        >
                          Deck: {{ gameSetup.villain.deck }}
                        </div>
                        <v-list-item-subtitle>
                          {{ gameSetup.villain.set }}
                        </v-list-item-subtitle>
                      </v-col>
                    </v-row>
                  </v-list-item>
                  <v-list-item
                    v-if="gameSetup.env"
                    variant="outlined"
                    class="rounded-shaped bg-purple-darken-2"
                  >
                    <v-row>
                      <v-col cols="3" md="2">
                        <v-btn
                          size="small"
                          @click="newEnv"
                          :color="randomButton"
                        >
                          <v-icon icon="mdi-shuffle"></v-icon>
                        </v-btn>
                      </v-col>
                      <v-col>
                        <v-list-item-title>
                          {{ gameSetup.env.name }}
                        </v-list-item-title>
                        <v-list-item-subtitle>
                          {{ gameSetup.env.set }}
                        </v-list-item-subtitle>
                      </v-col>
                    </v-row>
                  </v-list-item>
                  <v-list-item
                    v-for="(hero, index) in gameSetup.heroes"
                    :key="hero.name"
                    variant="outlined"
                    class="rounded-shaped bg-blue-darken-2"
                  >
                    <v-row>
                      <v-col cols="3" md="2">
                        <v-btn
                          size="small"
                          @click="newHero(index)"
                          :color="randomButton"
                        >
                          <v-icon icon="mdi-shuffle"></v-icon>
                        </v-btn>
                        <v-btn
                          size="small"
                          @click="newVariant(index)"
                          :color="randomButton2"
                        >
                          <v-icon icon="mdi-shuffle-variant"></v-icon>
                        </v-btn>
                      </v-col>
                      <v-col>
                        <v-list-item-title>
                          {{ hero.name }}
                        </v-list-item-title>
                        <div>
                          {{ hero.variant }}
                        </div>
                        <v-list-item-subtitle>
                          {{ hero.set }}
                        </v-list-item-subtitle>
                      </v-col>
                    </v-row>
                  </v-list-item>
                </v-list>
              </v-card-text>
            </v-card>
          </v-col>
        </v-row>
      </v-container>
    </v-main>
    <v-bottom-navigation grow multiple v-model="bottomNav" density="compact">
      <v-btn variant="flat" color="error" @click="reset">
        <v-icon icon="mdi-cancel"></v-icon>
      </v-btn>
      <v-btn variant="flat" color="success" @click="randomize">
        <v-icon icon="mdi-shuffle"></v-icon>
      </v-btn>
    </v-bottom-navigation>
  </v-app>
</template>

<script setup>
import { ref, computed, onMounted } from "vue";
const defaultSetting = {
  villains: [
    {
      name: "Akash'Bhuta",
      deck: "Akash'Bhuta",
      set: "Core Game",
      active: true,
    },
    {
      name: "Akash'Mecha",
      deck: "Akash'Bhuta",
      set: "Core Game",
      active: true,
    },
    {
      name: "Baron Blade",
      deck: "Baron Blade",
      set: "Core Game",
      active: true,
    },
    {
      name: "Mad Bomber Baron Blade",
      deck: "Baron Blade",
      set: "Core Game",
      active: true,
    },
    {
      name: "Citizen Dawn",
      deck: "Citizen Dawn",
      set: "Core Game",
      active: true,
    },
    {
      name: "Sunrise Citizen Dawn",
      deck: "Citizen Dawn",
      set: "Core Game",
      active: true,
    },
    {
      name: "Grand Warlord Voss",
      deck: "Grand Warlord Voss",
      set: "Core Game",
      active: true,
    },
    {
      name: "Censor",
      deck: "Grand Warlord Voss",
      set: "Core Game",
      active: true,
    },
    {
      name: "The Matriarch",
      deck: "The Matriarch",
      set: "Core Game",
      active: true,
    },
    {
      name: "The Mocktriarch",
      deck: "The Matriarch",
      set: "Core Game",
      active: true,
    },
    {
      name: "Omnitron",
      deck: "Omnitron",
      set: "Core Game",
      active: true,
    },
    {
      name: "Cosmic Omnitron",
      deck: "Omnitron",
      set: "Core Game",
      active: true,
    },
    {
      name: "Ambuscade",
      deck: "Ambuscade",
      set: "Rook City Renegades",
      active: true,
    },
    {
      name: "Vainglorious Ambuscade",
      deck: "Ambuscade",
      set: "Rook City Renegades",
      active: true,
    },
    {
      name: "Apex",
      deck: "Apex",
      set: "Rook City Renegades",
      active: true,
    },
    {
      name: "Blood-Leashed Apex",
      deck: "Apex",
      set: "Rook City Renegades",
      active: true,
    },
    {
      name: "The Fey-Court",
      deck: "The Fey-Court",
      set: "Rook City Renegades",
      active: true,
    },
    {
      name: "War-Girded Dagda & Morrigan",
      deck: "The Fey-Court",
      set: "Rook City Renegades",
      active: true,
    },
    {
      name: "GloomWeaver",
      deck: "GloomWeaver",
      set: "Rook City Renegades",
      active: true,
    },
    {
      name: "Soultaker GloomWeaver",
      deck: "GloomWeaver",
      set: "Rook City Renegades",
      active: true,
    },
    {
      name: "Kismet",
      deck: "Kismet",
      set: "Rook City Renegades",
      active: true,
    },
    {
      name: "Empowered Kismet",
      deck: "Kismet",
      set: "Rook City Renegades",
      active: true,
    },
    {
      name: "The Organization",
      deck: "The Organization",
      set: "Rook City Renegades",
      active: true,
    },
    {
      name: "The Bear",
      deck: "The Organization",
      set: "Rook City Renegades",
      active: true,
    },
    {
      name: "Plague Rat",
      deck: "Plague Rat",
      set: "Rook City Renegades",
      active: true,
    },
    {
      name: "Doctor Toxica",
      deck: "Plague Rat",
      set: "Rook City Renegades",
      active: true,
    },
    {
      name: "Spite",
      deck: "Spite",
      set: "Rook City Renegades",
      active: true,
    },
    {
      name: "Abomination Spite",
      deck: "Spite",
      set: "Rook City Renegades",
      active: true,
    },
    {
      name: "Terrorform",
      deck: "Terrorform",
      set: "Rook City Renegades",
      active: true,
    },
    {
      name: "Terrorform Mark III",
      deck: "Terrorform",
      set: "Rook City Renegades",
      active: true,
    },
  ],
  env: [
    {
      name: "Freedom Tower",
      set: "Core Game",
      active: true,
    },
    {
      name: "Insula Primalis",
      set: "Core Game",
      active: true,
    },
    {
      name: "Magmaria",
      set: "Core Game",
      active: true,
    },
    {
      name: "Megalopolis",
      set: "Core Game",
      active: true,
    },
    {
      name: "The Ruins of Atlantis",
      set: "Core Game",
      active: true,
    },
    {
      name: "Wagner Mars Base",
      set: "Core Game",
      active: true,
    },
    {
      name: "Diamond Manor",
      set: "Rook City Renegades",
      active: true,
    },
    {
      name: "Pike Industrial Complex",
      set: "Rook City Renegades",
      active: true,
    },
    {
      name: "The Realm of Discord",
      set: "Rook City Renegades",
      active: true,
    },
    {
      name: "Rook City",
      set: "Rook City Renegades",
      active: true,
    },
    {
      name: "The Temple of Zhu Long",
      set: "Rook City Renegades",
      active: true,
    },
  ],
  heroes: [
    {
      name: "Absolute Zero",
      variants: [
        {
          name: "Base",
          set: "Core Game",
          active: true,
        },
        {
          name: "First Appearance",
          set: "Core Game",
          active: true,
        },
      ],
      set: "Core Game",
      active: true,
    },
    {
      name: "The Argent Adept",
      variants: [
        {
          name: "Base",
          set: "Core Game",
          active: true,
        },
        {
          name: "First Appearance",
          set: "Core Game",
          active: true,
        },
      ],
      set: "Core Game",
      active: true,
    },
    {
      name: "Bunker",
      variants: [
        {
          name: "Base",
          set: "Core Game",
          active: true,
        },
        {
          name: "First Appearance",
          set: "Core Game",
          active: true,
        },
        {
          name: "Stealth Suit",
          set: "Rook City Renegades",
          active: true,
        },
      ],
      set: "Core Game",
      active: true,
    },
    {
      name: "Captain Cosmic",
      variants: [
        {
          name: "Base",
          set: "Core Game",
          active: true,
        },
        {
          name: "First Appearance",
          set: "Core Game",
          active: true,
        },
      ],
      set: "Core Game",
      active: true,
    },
    {
      name: "Fanatic",
      variants: [
        {
          name: "Base",
          set: "Core Game",
          active: true,
        },
        {
          name: "First Appearance",
          set: "Core Game",
          active: true,
        },
        {
          name: "Haunted",
          set: "Rook City Renegades",
          active: true,
        },
      ],
      set: "Core Game",
      active: true,
    },
    {
      name: "Haka",
      variants: [
        {
          name: "Base",
          set: "Core Game",
          active: true,
        },
        {
          name: "First Appearance",
          set: "Core Game",
          active: true,
        },
        {
          name: "Werewolf",
          set: "Rook City Renegades",
          active: true,
        },
      ],
      set: "Core Game",
      active: true,
    },
    {
      name: "Legacy",
      variants: [
        {
          name: "Base",
          set: "Core Game",
          active: true,
        },
        {
          name: "First Appearance",
          set: "Core Game",
          active: true,
        },
      ],
      set: "Core Game",
      active: true,
    },
    {
      name: "Ra",
      variants: [
        {
          name: "Base",
          set: "Core Game",
          active: true,
        },
        {
          name: "First Appearance",
          set: "Core Game",
          active: true,
        },
        {
          name: "Backdraft",
          set: "Rook City Renegades",
          active: true,
        },
      ],
      set: "Core Game",
      active: true,
    },
    {
      name: "Tachyon",
      variants: [
        {
          name: "Base",
          set: "Core Game",
          active: true,
        },
        {
          name: "First Appearance",
          set: "Core Game",
          active: true,
        },
      ],
      set: "Core Game",
      active: true,
    },
    {
      name: "Tempest",
      variants: [
        {
          name: "Base",
          set: "Core Game",
          active: true,
        },
        {
          name: "First Appearance",
          set: "Core Game",
          active: true,
        },
      ],
      set: "Core Game",
      active: true,
    },
    {
      name: "Unity",
      variants: [
        {
          name: "Base",
          set: "Core Game",
          active: true,
        },
        {
          name: "First Appearance",
          set: "Core Game",
          active: true,
        },
        {
          name: "Scavenger",
          set: "Rook City Renegades",
          active: true,
        },
      ],
      set: "Core Game",
      active: true,
    },
    {
      name: "The Wraith",
      variants: [
        {
          name: "Base",
          set: "Core Game",
          active: true,
        },
        {
          name: "First Appearance",
          set: "Core Game",
          active: true,
        },
        {
          name: "Detective",
          set: "Rook City Renegades",
          active: true,
        },
      ],
      set: "Core Game",
      active: true,
    },
    {
      name: "Alpha",
      variants: [
        {
          name: "Base",
          set: "Rook City Renegades",
          active: true,
        },
        {
          name: "First Appearance",
          set: "Rook City Renegades",
          active: true,
        },
        {
          name: "Reporter",
          set: "Rook City Renegades",
          active: true,
        },
        {
          name: "2000",
          set: "Rook City Renegades",
          active: true,
        },
      ],
      set: "Rook City Renegades",
      active: true,
    },
    {
      name: "Expatriette",
      variants: [
        {
          name: "Base",
          set: "Rook City Renegades",
          active: true,
        },
        {
          name: "First Appearance",
          set: "Rook City Renegades",
          active: true,
        },
        {
          name: "Dark Watch",
          set: "Rook City Renegades",
          active: true,
        },
        {
          name: "Eclipse",
          set: "Rook City Renegades",
          active: true,
        },
      ],
      set: "Rook City Renegades",
      active: true,
    },
    {
      name: "The Harpy",
      variants: [
        {
          name: "Base",
          set: "Rook City Renegades",
          active: true,
        },
        {
          name: "First Appearance",
          set: "Rook City Renegades",
          active: true,
        },
        {
          name: "Dark Watch",
          set: "Rook City Renegades",
          active: true,
        },
        {
          name: "Blood Raven",
          set: "Rook City Renegades",
          active: true,
        },
      ],
      set: "Rook City Renegades",
      active: true,
    },
    {
      name: "Mr. Fixer",
      variants: [
        {
          name: "Base",
          set: "Rook City Renegades",
          active: true,
        },
        {
          name: "First Appearance",
          set: "Rook City Renegades",
          active: true,
        },
        {
          name: "Dark Watch",
          set: "Rook City Renegades",
          active: true,
        },
        {
          name: "Black Fist",
          set: "Rook City Renegades",
          active: true,
        },
      ],
      set: "Rook City Renegades",
      active: true,
    },
    {
      name: "NightMist",
      variants: [
        {
          name: "Base",
          set: "Rook City Renegades",
          active: true,
        },
        {
          name: "First Appearance",
          set: "Rook City Renegades",
          active: true,
        },
        {
          name: "Dark Watch",
          set: "Rook City Renegades",
          active: true,
        },
        {
          name: "Mentor",
          set: "Rook City Renegades",
          active: true,
        },
      ],
      set: "Rook City Renegades",
      active: true,
    },
    {
      name: "Setback",
      variants: [
        {
          name: "Base",
          set: "Rook City Renegades",
          active: true,
        },
        {
          name: "First Appearance",
          set: "Rook City Renegades",
          active: true,
        },
        {
          name: "Dark Watch",
          set: "Rook City Renegades",
          active: true,
        },
        {
          name: "Fey-Cursed",
          set: "Rook City Renegades",
          active: true,
        },
      ],
      set: "Rook City Renegades",
      active: true,
    },
  ],
  sets: [
    {
      name: "Core Game",
      active: true,
    },
    {
      name: "Rook City Renegades",
      active: true,
    },
  ],
};

const randomButton = ref("success");
const randomButton2 = ref("secondary");
const clearColor = ref("");
const selectColor = ref("");
const textSettings = ref("");
const dialog = ref(false);
const settingsTab = ref("Villains");
const settingsTabItems = ref(["Villains", "Env", "Heroes", "Sets", "Settings"]);
const sets = ref([]);
const advanced = ref(false);
const event = ref(false);
const heroes = ref([]);
const env = ref([]);
const villains = ref([]);
const H = ref("3");
const gameSetup = ref({
  heroes: [],
  villain: "",
  env: "",
  advanced: false,
  event: false,
});
const lock = ref({
  heroes: [],
  villain: false,
  env: false,
});

const inactiveGame = computed(() => {
  return gameSetup.value.villain == "";
});
const activeHeroes = computed(() => {
  return heroes.value.filter((hero) => {
    return (
      hero.active &&
      hero.variants.filter((variant) => {
        return variant.active && activeSet(variant.set);
      }).length > 0 &&
      activeSet(hero.set)
    );
  });
});
const activeSets = computed(() => {
  return sets.value
    .filter((set) => {
      return set.active;
    })
    .map((set) => {
      return set.name;
    });
});
const bottomNav = computed({
  get() {
    return [0, 1, 2];
  },
  set(newValue) {},
});
const settings = computed({
  get() {
    return {
      villains: villains.value,
      env: env.value,
      heroes: heroes.value,
      sets: sets.value,
    };
  },
  set(newValue) {
    if (newValue) {
      replaceSettings(newValue);
    } else {
      defaultSettings();
    }
  },
});

const getRandomInt = (min, max) => {
  min = Math.ceil(min);
  max = Math.floor(max);
  return Math.floor(Math.random() * (max - min + 1)) + min;
};

const randomize = () => {
  let Heroes;
  if (H.value == "?") {
    Heroes = getRandomInt(3, 5);
  } else {
    Heroes = parseInt(H.value);
  }
  gameSetup.value.heroes = shuffle(activeHeroes.value)
    .slice(0, Heroes)
    .map((hero) => {
      let variantSelected = shuffle(activeList(hero.variants))[0];
      return {
        name: hero.name,
        variant: variantSelected.name,
        set: [...new Set([hero.set, variantSelected.set])].toString(),
      };
    });
  gameSetup.value.env = shuffle(activeList(env.value))[0];
  gameSetup.value.villain = shuffle(activeList(villains.value))[0];
  randomAdvanceEvent();
};
const randomAdvanceEvent = () => {
  if (advanced.value == "random") {
    gameSetup.value.advanced = getRandomInt(0, 1) == 1;
  } else {
    gameSetup.value.advanced = advanced.value;
  }
  if (gameSetup.value.villain.name != gameSetup.value.villain.deck) {
    gameSetup.value.event = false;
  } else if (event.value == "random") {
    gameSetup.value.event = getRandomInt(0, 1) == 1;
  } else {
    gameSetup.value.event = event.value;
  }
};
const shuffle = (array) => {
  for (let i = array.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [array[i], array[j]] = [array[j], array[i]];
  }
  return array;
};
const activeList = (variants) => {
  return variants.filter((variant) => {
    return variant.active && activeSet(variant.set);
  });
};
const newHero = (index) => {
  let newHero = shuffle(
    activeHeroes.value.filter((hero) => {
      return (
        gameSetup.value.heroes.findIndex((heroIndex) => {
          return heroIndex.name == hero.name;
        }) == -1
      );
    })
  )[0];
  let variantSelected = shuffle(activeList(newHero.variants))[0];
  gameSetup.value.heroes[index] = {
    name: newHero.name,
    variant: shuffle(activeList(newHero.variants))[0].name,
    set: [...new Set([newHero.set, variantSelected.set])].toString(),
  };
};
const newVariant = (index) => {
  let selectedHero = heroes.value.find((hero) => {
    return hero.name == gameSetup.value.heroes[index].name;
  });
  let selectedHeroVariants = selectedHero.variants.filter((variant) => {
    return variant.name != gameSetup.value.heroes[index].variant;
  });

  let variant = shuffle(activeList(selectedHeroVariants))[0];
  gameSetup.value.heroes[index].variant = variant.name;
  gameSetup.value.heroes[index].set = [
    ...new Set([selectedHero.set, variant.set]),
  ].toString();
};
const newEnv = () => {
  let newEnvList = env.value.filter((item) => {
    return item.name != gameSetup.value.env.name;
  });
  gameSetup.value.env = shuffle(activeList(newEnvList))[0];
};
const newVillain = () => {
  let newVillainList = villains.value.filter((item) => {
    return item.name != gameSetup.value.villain.name;
  });
  gameSetup.value.villain = shuffle(activeList(newVillainList))[0];
  randomAdvanceEvent();
};
const reset = () => {
  gameSetup.value = {
    heroes: [],
    villain: "",
    env: "",
  };
};
const clear = (selection) => {
  selection.forEach((item, index) => {
    selection[index].active = false;
    if (item.variants) {
      item.variants.forEach((variants, vindex) => {
        selection[index].variants[vindex].active = false;
      });
    }
  });
};
const selectAll = (selection) => {
  selection.forEach((item, index) => {
    selection[index].active = true;
    if (item.variants) {
      item.variants.forEach((variants, vindex) => {
        selection[index].variants[vindex].active = true;
      });
    }
  });
};
const activeSet = (set) => {
  return activeSets.value.includes(set);
};
const save = () => {
  dialog.value = false;
  localStorage.setItem("settings", JSON.stringify(settings.value));
};
const loadSettings = () => {
  try {
    settings.value = JSON.parse(textSettings.value);
    reloadSettings();
  } catch (e) {
    return false;
  }
};
const reloadSettings = () => {
  textSettings.value = JSON.stringify(settings.value, undefined, 4);
};
const defaultSettings = () => {
  replaceSettings(JSON.parse(JSON.stringify(defaultSetting)));
  reloadSettings();
};
const replaceSettings = (newValue) => {
  villains.value = newValue.villains;
  env.value = newValue.env;
  heroes.value = newValue.heroes;
  sets.value = newValue.sets;
};
const additiveSettings = (newValue) => {
  villains.value.forEach((item) => {
    let index = newValue.villains.findIndex((villain) => {
      return villain.name == item.name;
    });
    if (index > -1) {
      villains.value[index] = item;
    } else {
      newValue.villains.push(item);
    }
  });
  villains.value = newValue.villains;
  env.value.forEach((item) => {
    let index = newValue.env.findIndex((envDeck) => {
      return envDeck.name == item.name;
    });
    if (index > -1) {
      newValue.env[index] = item;
    } else {
      newValue.env.push(item);
    }
  });
  env.value = newValue.env;
  heroes.value.forEach((item) => {
    let index = newValue.heroes.findIndex((hero) => {
      return hero.name == item.name;
    });
    if (index > -1) {
      item.variants.forEach((subitem) => {
        let i = newValue.heroes[index].variants.findIndex((variant) => {
          return variant.name == subitem.name;
        });
        if (i > -1) {
          newValue.heroes[index].variants[i] = subitem;
        } else {
          newValue.heroes[index].variants.push(subitem);
        }
      });

      newValue.heroes[index].active = item.active;
    } else {
      newValue.heroes.push(item);
    }
  });
  heroes.value = newValue.heroes;
  sets.value.forEach((item) => {
    let index = newValue.sets.findIndex((set) => {
      return set.name == item.name;
    });
    if (index > -1) {
      newValue.sets[index] = item;
    } else {
      newValue.sets.push(item);
    }
  });
  sets.value = newValue.sets;
};

onMounted(() => {
  settings.value = JSON.parse(localStorage.getItem("settings"));
  textSettings.value = JSON.stringify(settings.value, undefined, 4);
});
</script>
