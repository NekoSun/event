<template>
  <div class="row pb-4">
    <div class="col-md-1" />
    <div class="col-md-10 position-static">
      <v-app>
        <v-row>
          <v-col>
            <v-sheet>
              <v-toolbar
                class="hidden-xs-only"
                dense
                dark
                color="primary"
                flat
              >
                <v-app-bar-nav-icon
                  class="hidden-sm-and-down"
                  @click="clickBarNav"
                />
                <v-tooltip
                  bottom
                >
                  <template
                    v-slot:activator="{ on }"
                  >
                    <v-btn
                      outlined
                      class="mr-4 hidden-sm-and-down"
                      @click="setToday"
                      v-on="on"
                    >
                      Сегодня
                    </v-btn>
                  </template>
                  <span>{{ tooltipTitle }}</span>
                </v-tooltip>
                <v-btn fab text small @click="prev">
                  <v-icon small>
                    mdi-chevron-left
                  </v-icon>
                </v-btn>
                <v-btn fab text small @click="next">
                  <v-icon small>
                    mdi-chevron-right
                  </v-icon>
                </v-btn>
                <v-toolbar-title class="hidden-sm-and-down">
                  {{ title }}
                </v-toolbar-title>
                <v-spacer />
                <v-menu
                  offset-y
                  :close-on-content-click="false"
                >
                  <template v-slot:activator="{ on }">
                    <v-text-field
                      v-model="inputFiltr"
                      label="Фильтр по специализации"
                      outlined
                      dense
                      hide-details
                      :append-icon="focused ? 'mdi-menu-up' : 'mdi-menu-down'"
                      class="mr-4"
                      v-on="on"
                      @focus="onFocus"
                      @blur="onBlur"
                    />
                  </template>
                  <v-container
                    fluid
                    style="background: white;"
                  >
                    <v-row
                      :style="{width: rowWidthFiltr}"
                      style="position: relative; height: 200px;"
                    >
                      <v-col :cols="colsS" style="margin-right: -24px; position: absolute; left: 0; top: 0; bottom: 0; right: 50%; overflow: auto; overflow-x: hidden;">
                        <v-list-item-group
                          multiple
                          @change="changeSpecializations"
                        >
                          <template v-for="(item, i) of filteredSpecializationsList">
                            <v-list-item
                              :key="i"
                              :value="item.value"
                              :style="{ marginLeft: -16 + 'px', marginRight: -16 + 'px'}"
                            >
                              <template v-slot:default="{ active, toggle }">
                                <v-list-item-action>
                                  <v-checkbox
                                    :input-value="active"
                                    :true-value="item"
                                    @click="toggle"
                                  />
                                </v-list-item-action>
                                <v-list-item-content>
                                  <v-list-item-title v-text="item.label" />
                                </v-list-item-content>
                              </template>
                            </v-list-item>
                          </template>
                        </v-list-item-group>
                      </v-col>
                      <v-col
                        v-if="filtersEvent.specialists.length !== 0"
                        cols="6"
                        style="position: absolute; left: 50%; top: 0; bottom: 0; right: 0; overflow: auto; overflow-x: hidden;"
                      >
                        <v-list-item-group
                          v-model="selectedSpecialists"
                          multiple
                          style="width: 100%"
                          dark
                        >
                          <template v-for="(item, i) in filtersEvent.specialists">
                            <v-list-item
                              :key="item.value"
                              :value="item.value"
                              :style="{ backgroundColor: item.color, marginLeft: -16 + 'px', marginRight: -16 + 'px'}"
                            >
                              <template v-slot:default="{ active, toggle }">
                                <v-list-item-action>
                                  <v-checkbox
                                    :input-value="active"
                                  />
                                </v-list-item-action>

                                <v-list-item-content>
                                  <v-list-item-title v-text="item.label" />
                                </v-list-item-content>
                              </template>
                            </v-list-item>
                          </template>
                        </v-list-item-group>
                      </v-col>
                    </v-row>
                  </v-container>
                </v-menu>
                <v-menu
                  offset-y
                  :close-on-content-click="false"
                >
                  <template v-slot:activator="{ on }">
                    <v-text-field
                      v-model="inputFiltrD"
                      label="Фильтр по специалистам"
                      outlined
                      dense
                      hide-details
                      :append-icon="focused ? 'mdi-menu-up' : 'mdi-menu-down'"
                      class="mr-4"
                      v-on="on"
                      @focus="onFocus"
                      @blur="onBlur"
                    />
                  </template>
                  <v-container fluid style="background: white;">
                    <v-row
                      :style="{width: rowWidthFiltrD}"
                      style="position: relative; height: 200px;"
                    >
                      <v-col :cols="colsD" style="margin-right: -24px; position: absolute; left: 0; top: 0; bottom: 0; right: 50%; overflow: auto; overflow-x: hidden;">
                        <v-list-item-group
                          multiple
                          style="width: 100%"
                          dark
                          @change="changeSpecialists"
                        >
                          <template v-for="(item, i) of filteredSpecialistList">
                            <v-list-item
                              :id="i"
                              :key="i"
                              :value="item.value"
                              :style="{backgroundColor: item.color, marginLeft: -16 + 'px', marginRight: -16 + 'px'}"
                            >
                              <template v-slot:default="{ active, toggle }">
                                <v-list-item-action>
                                  <v-checkbox
                                    :input-value="active"
                                    :true-value="item"
                                    @click="toggle"
                                  />
                                </v-list-item-action>
                                <v-list-item-content>
                                  <v-list-item-title v-text="item.label" />
                                </v-list-item-content>
                              </template>
                            </v-list-item>
                          </template>
                        </v-list-item-group>
                      </v-col>
                      <v-col
                        v-if="filtersEvent.specializations.length !== 0"
                        cols="6"
                        style="position: absolute; left: 50%; top: 0; bottom: 0; right: 0; overflow: auto; overflow-x: hidden;"
                      >
                        <v-list-item-group
                          v-model="selectedSpecializationsList"
                          multiple
                          style="width: 100%"
                        >
                          <template v-for="(item, i) of filtersEvent.specializations">
                            <v-list-item
                              :key="item.value"
                              :value="item.value"
                              :style="{marginLeft: -16 + 'px', marginRight: -16 + 'px'}"
                            >
                              <template v-slot:default="{ active, toggle }">
                                <v-list-item-action>
                                  <v-checkbox
                                    :input-value="active"
                                  />
                                </v-list-item-action>

                                <v-list-item-content>
                                  <v-list-item-title v-text="item.label" />
                                </v-list-item-content>
                              </template>
                            </v-list-item>
                          </template>
                        </v-list-item-group>
                      </v-col>
                    </v-row>
                  </v-container>
                </v-menu>
                <v-menu
                  transition="slide-y-transition"
                >
                  <template v-slot:activator="{ on }">
                    <v-btn
                      outlined
                      class="hidden-xs-only"
                      v-on="on"
                    >
                      <span>{{ typeToLabel[type] }}</span>
                      <v-icon right>
                        mdi-menu-down
                      </v-icon>
                    </v-btn>
                  </template>
                  <v-list>
                    <v-list-item
                      v-for="(item, i) in items"
                      :key="i"
                      @click="clickBarNav2(item)"
                    >
                      <v-list-item-content>
                        <v-list-item-title v-text="item.text" />
                      </v-list-item-content>
                    </v-list-item>
                  </v-list>
                </v-menu>
              </v-toolbar>
              <v-row
                class="d-flex d-sm-none"
              >
                <v-col cols="12">
                  <v-menu
                    offset-y
                    :close-on-content-click="false"
                  >
                    <template v-slot:activator="{ on }">
                      <v-text-field
                        v-model="inputFiltr"
                        label="Фильтр по специализации"
                        outlined
                        dense
                        hide-details
                        :append-icon="focused ? 'mdi-menu-up' : 'mdi-menu-down'"
                        class="mb-4"
                        v-on="on"
                        @focus="onFocus"
                        @blur="onBlur"
                      />
                    </template>
                    <v-container
                      fluid
                      style="background: white;"
                    >
                      <v-row
                        :style="{width: rowWidthFiltrM}"
                        style="position: relative; height: 200px;"
                      >
                        <v-col :cols="colsS" style="margin-right: -24px; position: absolute; left: 0; top: 0; bottom: 0; right: 50%; overflow: auto; overflow-x: hidden;">
                          <v-list-item-group
                            multiple
                            @change="changeSpecializations"
                          >
                            <template v-for="(item, i) of filteredSpecializationsList">
                              <v-list-item
                                :key="i"
                                :value="item.value"
                                :style="{ marginLeft: -16 + 'px', marginRight: -16 + 'px'}"
                              >
                                <template v-slot:default="{ active, toggle }">
                                  <v-list-item-action>
                                    <v-checkbox
                                      :input-value="active"
                                      :true-value="item"
                                      @click="toggle"
                                    />
                                  </v-list-item-action>
                                  <v-list-item-content class="ml-n7">
                                    <v-list-item-title v-text="item.label" />
                                  </v-list-item-content>
                                </template>
                              </v-list-item>
                            </template>
                          </v-list-item-group>
                        </v-col>
                        <v-col
                          v-if="filtersEvent.specialists.length !== 0"
                          cols="6"
                          style="position: absolute; left: 50%; top: 0; bottom: 0; right: 0; overflow: auto; overflow-x: hidden;"
                        >
                          <v-list-item-group
                            v-model="selectedSpecialists"
                            multiple
                            style="width: 100%"
                            dark
                          >
                            <template v-for="(item, i) in filtersEvent.specialists">
                              <v-list-item
                                :key="item.value"
                                :value="item.value"
                                :style="{ backgroundColor: item.color, marginLeft: -16 + 'px', marginRight: -16 + 'px'}"
                              >
                                <template v-slot:default="{ active, toggle }">
                                  <v-list-item-action>
                                    <v-checkbox
                                      :input-value="active"
                                    />
                                  </v-list-item-action>

                                  <v-list-item-content class="ml-n7">
                                    <v-list-item-title v-text="item.label" />
                                  </v-list-item-content>
                                </template>
                              </v-list-item>
                            </template>
                          </v-list-item-group>
                        </v-col>
                      </v-row>
                    </v-container>
                  </v-menu>
                  <v-menu
                    offset-y
                    :close-on-content-click="false"
                  >
                    <template v-slot:activator="{ on }">
                      <v-text-field
                        v-model="inputFiltrD"
                        label="Фильтр по специалистам"
                        outlined
                        dense
                        hide-details
                        :append-icon="focused ? 'mdi-menu-up' : 'mdi-menu-down'"
                        class="mb-4"
                        v-on="on"
                        @focus="onFocus"
                        @blur="onBlur"
                      />
                    </template>
                    <v-container fluid style="background: white;">
                      <v-row
                        :style="{width: rowWidthFiltrDM}"
                        style="position: relative; height: 200px;"
                      >
                        <v-col :cols="colsD" style="margin-right: -24px; position: absolute; left: 0; top: 0; bottom: 0; right: 50%; overflow: auto; overflow-x: hidden;">
                          <v-list-item-group
                            multiple
                            style="width: 100%"
                            dark
                            @change="changeSpecialists"
                          >
                            <template v-for="(item, i) of filteredSpecialistList">
                              <v-list-item
                                :id="i"
                                :key="i"
                                :value="item.value"
                                :style="{backgroundColor: item.color, marginLeft: -16 + 'px', marginRight: -16 + 'px'}"
                              >
                                <template v-slot:default="{ active, toggle }">
                                  <v-list-item-action>
                                    <v-checkbox
                                      :input-value="active"
                                      :true-value="item"
                                      @click="toggle"
                                    />
                                  </v-list-item-action>
                                  <v-list-item-content class="ml-n7">
                                    <v-list-item-title v-text="item.label" />
                                  </v-list-item-content>
                                </template>
                              </v-list-item>
                            </template>
                          </v-list-item-group>
                        </v-col>
                        <v-col
                          v-if="filtersEvent.specializations.length !== 0"
                          cols="6"
                          style="position: absolute; left: 50%; top: 0; bottom: 0; right: 0; overflow: auto; overflow-x: hidden;"
                        >
                          <v-list-item-group
                            v-model="selectedSpecializationsList"
                            multiple
                            style="width: 100%"
                          >
                            <template v-for="(item, i) of filtersEvent.specializations">
                              <v-list-item
                                :key="item.value"
                                :value="item.value"
                                :style="{marginLeft: -16 + 'px', marginRight: -16 + 'px'}"
                              >
                                <template v-slot:default="{ active, toggle }">
                                  <v-list-item-action>
                                    <v-checkbox
                                      :input-value="active"
                                    />
                                  </v-list-item-action>

                                  <v-list-item-content class="ml-n7">
                                    <v-list-item-title v-text="item.label" />
                                  </v-list-item-content>
                                </template>
                              </v-list-item>
                            </template>
                          </v-list-item-group>
                        </v-col>
                      </v-row>
                    </v-container>
                  </v-menu>
                  <v-btn fab text small @click="prev">
                    <v-icon small>
                      mdi-chevron-left
                    </v-icon>
                  </v-btn>
                  <v-btn fab text small @click="next">
                    <v-icon small>
                      mdi-chevron-right
                    </v-icon>
                  </v-btn>
                  <v-menu
                    transition="slide-y-transition"
                  >
                    <template v-slot:activator="{ on }">
                      <v-btn
                        outlined
                        class="mb-4"
                        v-on="on"
                      >
                        <span>{{ typeToLabel[type] }}</span>
                        <v-icon right>
                          mdi-menu-down
                        </v-icon>
                      </v-btn>
                    </template>
                    <v-list>
                      <v-list-item
                        v-for="(item, i) in items"
                        :key="i"
                        @click="clickBarNav2(item)"
                      >
                        <v-list-item-content>
                          <v-list-item-title v-text="item.text" />
                        </v-list-item-content>
                      </v-list-item>
                    </v-list>
                  </v-menu>
                </v-col>
              </v-row>
            </v-sheet>
            <v-sheet>
              <v-row>
                <v-slide-x-transition>
                  <v-col
                    v-if="drawer"
                    lg="3"
                  >
                    <v-list>
                      <v-list-item>
                        <v-date-picker
                          v-model="picker"
                          first-day-of-week="1"
                          year-icon="mdi-calendar-blank"
                          prev-icon="mdi-skip-previous"
                          next-icon="mdi-skip-next"
                          range
                          @input="clickDate2"
                        >
                          <v-btn
                            text
                            color="primary"
                            @click="clearCalendar"
                          >
                            Очистить
                          </v-btn>
                        </v-date-picker>
                      </v-list-item>
                    </v-list>
                    <v-list />
                  </v-col>
                </v-slide-x-transition>
                <v-col>
                  <v-sheet height="700">
                    <v-calendar
                      ref="calendar"
                      v-model="focus"
                      color="primary"
                      weekdays="1, 2, 3, 4, 5, 6, 0"
                      event-overlap-mode="column"
                      :events="events"
                      :event-color="getEventColor"
                      :event-margin-bottom="3"
                      :type="type"
                      :min-weeks="1"
                      :start="dataStart"
                      :end="dataEnd"
                      @click:event="showEvent"
                      @click:more="viewDay"
                      @click:date="viewDay"
                      @change="updateRange"
                    />
                  </v-sheet>
                  <v-menu
                    v-model="selectedOpen"
                    :close-on-content-click="false"
                    :activator="selectedElement"
                    full-width
                    offset-x
                  >
                    <v-card
                      color="grey lighten-4"
                      min-width="350px"
                      flat
                    >
                      <v-toolbar
                        :color="selectedEvent.color"
                        dark
                      >
                        <v-toolbar-title v-html="selectedEvent.specialization" />
                        <v-spacer />
                      </v-toolbar>
                      <v-card-text>
                        <span v-html="selectedEvent.details" />
                      </v-card-text>
                      <v-card-actions>
                        <v-btn
                          text
                          color="secondary"
                          @click="selectedOpen = false"
                        >
                          Закрыть
                        </v-btn>
                        <v-spacer />
                      </v-card-actions>
                    </v-card>
                  </v-menu>
                </v-col>
              </v-row>
            </v-sheet>
          </v-col>
          <v-dialog
            v-model="dialogDelReason"
            fullscreen
            hide-overlay
            transition="dialog-bottom-transition"
            style="z-index: 1031"
          >
            <v-card tile>
              <v-toolbar
                flat
                dark
                color="primary"
              >
                <v-btn
                  icon
                  dark
                  @click="dialogDelReason = false"
                >
                  <v-icon>mdi-close</v-icon>
                </v-btn>
                <v-toolbar-title>Причина удаления события</v-toolbar-title>
                <v-spacer />
                <v-toolbar-items>
                  <v-btn
                    dark
                    text
                    @click="dialogDelReason = false"
                  >
                    Применить
                  </v-btn>
                </v-toolbar-items>
              </v-toolbar>
              <v-card-text>
                <text-editor :content="dialogDelReasonContent.info" @saveData="saveClinicInfo" />
              </v-card-text>

              <div style="flex: 1 1 auto;" />
            </v-card>
          </v-dialog>
        </v-row>
        <v-dialog
          v-model="addEvent.adSchedule"
          persistent
          max-width="600px"
          @keydown.esc="addEvent.adSchedule = false"
        >
          <template v-slot:activator="{ on }">
            <v-btn
              absolute
              dark
              fab
              bottom
              right
              color="pink"
              v-on="on"
            >
              <v-icon>
                mdi-plus
              </v-icon>
            </v-btn>
          </template>
          <v-card
            v-if="addEvent.adSchedule"
          >
            <v-form
              ref="form"
              v-model="addEvent.valid"
              lazy-validation
            >
              <v-card-title>
                <span class="headline">Добавить событие</span>
              </v-card-title>
              <v-card-text>
                <v-container>
                  <v-row>
                    <v-col cols="12" sm="6">
                      <v-autocomplete
                        v-model="addEvent.chosenSpecialization"
                        :items="addEvent.specialization"
                        label="Специализация"
                        item-text="label"
                        item-value="value"
                        clearable
                        required
                        :rules="[v => !!v || 'Укажите специализацию']"
                        @change="addEventChangeSpecialization"
                      />
                    </v-col>
                    <v-col cols="12" sm="6">
                      <v-autocomplete
                        v-model="addEvent.chosenSpecialist"
                        :items="addEvent.specialist"
                        label="Специалист"
                        item-text="label"
                        item-value="value"
                        clearable
                        required
                        :rules="[v => !!v || 'Выберете специалиста']"
                        @change="addEventChangeSpecialist"
                      />
                    </v-col>
                    <v-col cols="12" sm="6">
                      <v-menu
                        ref="menuDate"
                        v-model="addEvent.dateMenuStart"
                        :close-on-content-click="false"
                        :return-value.sync="addEvent.date"
                        transition="scale-transition"
                        offset-y
                        min-width="290px"
                      >
                        <template v-slot:activator="{ on }">
                          <v-text-field
                            :value="addEventComputedDateFormatted"
                            label="Дата события"
                            readonly
                            required
                            :disabled="addEvent.weekValues.length === 0 && addEvent.radioGroup === 'radio2'"
                            :rules="rulesDateMenuStart()"
                            v-on="on"
                          />
                        </template>
                        <v-date-picker
                          v-if="addEvent.dateMenuStart"
                          v-model="addEvent.date"
                          no-title
                          scrollable
                          first-day-of-week="1"
                          :min="today"
                          :max="addEvent.radioGroup === 'radio2' ? addEvent.dateEnd : null"
                          :allowed-dates="(addEvent.radioGroup === 'radio2') ? allowedDates : null"
                        >
                          <v-spacer />
                          <v-btn text color="primary" @click="addEvent.dateMenuStart = false">
                            Отмена
                          </v-btn>
                          <v-btn text color="primary" @click="$refs.menuDate.save(addEvent.date)">
                            Сохранить
                          </v-btn>
                        </v-date-picker>
                      </v-menu>
                    </v-col>
                    <v-col cols="12" sm="6">
                      <v-menu
                        ref="menuReception"
                        v-model="addEvent.menuReception"
                        :close-on-content-click="false"
                        :return-value.sync="addEvent.timeReception"
                        transition="scale-transition"
                        offset-y
                        min-width="290px"
                      >
                        <template v-slot:activator="{ on }">
                          <v-text-field
                            v-model="addEvent.timeReception"
                            label="Время приема"
                            readonly
                            required
                            :rules="[v => !!v || 'Выберете продолжительность приема']"
                            v-on="on"
                          />
                        </template>
                        <v-time-picker
                          v-if="addEvent.menuReception"
                          v-model="addEvent.timeReception"
                          format="24hr"
                          :allowed-minutes="m => m % 5 === 0"
                          :min="addEvent.timeReception2"
                        >
                          <v-spacer />
                          <v-btn text color="primary" @click="addEvent.menuReception = false">
                            Отмена
                          </v-btn>
                          <v-btn text color="primary" @click="$refs.menuReception.save(addEvent.timeReception)">
                            Сохранить
                          </v-btn>
                        </v-time-picker>
                      </v-menu>
                    </v-col>
                    <v-col cols="12" sm="6">
                      <v-menu
                        ref="menuTimeStart"
                        v-model="addEvent.menuTimeStart"
                        :close-on-content-click="false"
                        :return-value.sync="addEvent.timeStart"
                        transition="scale-transition"
                        offset-y
                        min-width="290px"
                      >
                        <template v-slot:activator="{ on }">
                          <v-text-field
                            v-model="addEvent.timeStart"
                            label="Начало дня"
                            readonly
                            required
                            :rules="[v => !!v || 'Выберете время начала']"
                            v-on="on"
                          />
                        </template>
                        <v-time-picker
                          v-if="addEvent.menuTimeStart"
                          v-model="addEvent.timeStart"
                          format="24hr"
                          :allowed-minutes="m => m % 5 === 0"
                          :max="addEvent.timeEnd !== null ? this.$moment(`${today} ${addEvent.timeEnd}`).add(-1, 'minutes').format('HH:mm') : null"
                        >
                          <v-spacer />
                          <v-btn text color="primary" @click="addEvent.menuTimeStart = false">
                            Отмена
                          </v-btn>
                          <v-btn text color="primary" @click="$refs.menuTimeStart.save(addEvent.timeStart)">
                            Сохранить
                          </v-btn>
                        </v-time-picker>
                      </v-menu>
                    </v-col>
                    <v-col cols="12" sm="6">
                      <v-menu
                        ref="menuTimeEnd"
                        v-model="addEvent.menuTimeEnd"
                        :close-on-content-click="false"
                        :return-value.sync="addEvent.timeEnd"
                        transition="scale-transition"
                        offset-y
                        min-width="290px"
                      >
                        <template v-slot:activator="{ on }">
                          <v-text-field
                            v-model="addEvent.timeEnd"
                            label="Конец дня"
                            readonly
                            required
                            :rules="[v => !!v || 'Выберете время завершения']"
                            v-on="on"
                          />
                        </template>
                        <v-time-picker
                          v-if="addEvent.menuTimeEnd"
                          v-model="addEvent.timeEnd"
                          format="24hr"
                          :allowed-minutes="m => m % 5 === 0"
                          :min="addEventTimeEnd()"
                        >
                          <v-spacer />
                          <v-btn text color="primary" @click="addEvent.menuTimeEnd = false">
                            Отмена
                          </v-btn>
                          <v-btn text color="primary" @click="$refs.menuTimeEnd.save(addEvent.timeEnd)">
                            Сохранить
                          </v-btn>
                        </v-time-picker>
                      </v-menu>
                    </v-col>
                    <v-col cols="12">
                      <v-radio-group
                        v-model="addEvent.radioGroup"
                        row
                      >
                        <v-radio
                          label="Не повторять"
                          value="radio1"
                          @change="addEvent.date = new Date().toISOString().substr(0, 10)"
                        />
                        <v-radio
                          label="Повторить"
                          value="radio2"
                          @change="addEvent.date = ''"
                        />
                      </v-radio-group>
                    </v-col>
                    <v-col
                      v-if="addEvent.radioGroup === 'radio2'"
                      cols="12"
                      sm="6"
                    >
                      <v-autocomplete
                        v-model="addEvent.weekValues"
                        :items="addEvent.week"
                        item-text="label"
                        item-value="value"
                        label="День недели"
                        multiple
                        clearable
                        required
                        :rules="[v => (v.length !== 0) || 'Выберете день надели']"
                      >
                        <template v-slot:selection="{ item, index }">
                          <v-chip
                            label
                            close
                            small
                            @click:close="removeWeeks(item)"
                          >
                            {{ item.min }}
                          </v-chip>
                        </template>
                        <template v-slot:prepend-item>
                          <v-list-item
                            ripple
                            @click="weekToggle"
                          >
                            <v-list-item-action>
                              <v-icon :color="addEvent.weekValues.length > 0 ? 'indigo darken-4' : ''">
                                {{ icon }}
                              </v-icon>
                            </v-list-item-action>
                            <v-list-item-content>
                              <v-list-item-title>Выбрать все</v-list-item-title>
                            </v-list-item-content>
                          </v-list-item>
                          <v-divider class="mt-2" />
                        </template>
                      </v-autocomplete>
                    </v-col>
                    <v-col
                      v-if="addEvent.radioGroup === 'radio2'"
                      cols="12"
                      sm="6"
                    >
                      <v-menu
                        ref="menuDataEnd"
                        v-model="addEvent.dateMenuEnd"
                        :close-on-content-click="false"
                        :return-value.sync="addEvent.dateEnd"
                        transition="scale-transition"
                        offset-y
                        min-width="290px"
                      >
                        <template v-slot:activator="{ on }">
                          <v-text-field
                            :value="addEventComputedDateEndFormatted"
                            label="Дата завершения повторения"
                            readonly
                            required
                            :rules="[v => !!v || 'Выберете дату завершения']"
                            v-on="on"
                          />
                        </template>
                        <v-date-picker
                          v-if="addEvent.dateMenuEnd"
                          v-model="addEvent.dateEnd"
                          no-title
                          scrollable
                          first-day-of-week="1"
                          :min="choiceStartEndDay()"
                          :allowed-dates="allowedDates"
                        >
                          <v-spacer />
                          <v-btn text color="primary" @click="addEvent.dateMenuEnd = false">
                            Отмена
                          </v-btn>
                          <v-btn text color="primary" @click="$refs.menuDataEnd.save(addEvent.dateEnd)">
                            Сохранить
                          </v-btn>
                        </v-date-picker>
                      </v-menu>
                    </v-col>
                  </v-row>
                </v-container>
              </v-card-text>
              <v-card-actions>
                <v-spacer />
                <v-btn
                  color="red"
                  depressed
                  @click="addEventDumping"
                >
                  Закрыть
                </v-btn>
                <v-btn
                  :disabled="!addEvent.valid"
                  color="green"
                  depressed
                  @click="addEventSubmit"
                >
                  Добавить
                </v-btn>
              </v-card-actions>
            </v-form>
          </v-card>
        </v-dialog>
      </v-app>
    </div>
  </div>
</template>

<script>
import { getList } from '@/api/specialist.js'
import { fetchScheduleSpecialists, addSchedule } from '@/api/scheduleEvent.js'
import { fetchSpecialization } from '@/api/clinic.js'
import ColorHash from 'color-hash'
const colorHash = new ColorHash()

export default {
  data: () => ({
    // item: 1,
    items: [
      { text: 'День', param: 'day' },
      { text: 'Неделя', param: 'week' },
      { text: 'Месяц', param: 'month' },
      { text: 'На 4 дня', param: '4day' }
    ],
    specialist: [],
    specialization: [],
    drawer: false,
    picker: [new Date().toISOString().substr(0, 10)],
    today: new Date().toISOString().substr(0, 10),
    focus: new Date().toISOString().substr(0, 10),
    type: 'month',
    typeToLabel: {
      month: 'Месяц',
      week: 'Неделя',
      day: 'День',
      '4day': 'На 4 дня'
    },
    start: null,
    end: null,
    dataStart: null,
    dataEnd: null,
    selectedEvent: {},
    selectedElement: null,
    selectedOpen: false,
    dialogDelReason: false,
    dialogDelReasonContent: {},
    events: [],
    filtersEvent: {
      specializationsModel: [],
      specialistsModel: [],
      changeSpecializations: [],
      changeSpecialists: [],
      specialists: [],
      specializations: []
      // selectedSpecialists: []
    },
    focused: false,
    inputFiltr: '',
    inputFiltrD: '',
    rowWidthFiltr: '',
    rowWidthFiltrM: '',
    rowWidthFiltrD: '',
    rowWidthFiltrDM: '',
    selectedSpecialists: [],
    selectedSpecializationsList: [],
    colsS: false,
    colsD: false,
    addEvent: {
      adSchedule: false,
      chosenSpecialization: null,
      chosenSpecialist: null,
      specialization: [],
      specialist: [],
      date: new Date().toISOString().substr(0, 10),
      dateEnd: null,
      dateMenuStart: false,
      dateMenuEnd: false,
      menuTimeStart: false,
      menuTimeEnd: false,
      menuReception: false,
      timeStart: null,
      timeEnd: null,
      timeReception: '00:30',
      timeReception2: '00:05',
      radioGroup: 'radio1',
      valid: true,
      week: [
        {
          label: 'понедельник',
          value: 'Mo',
          min: 'Пн'
        },
        {
          label: 'вторник',
          value: 'Tu',
          min: 'Вт'
        },
        {
          label: 'среда',
          value: 'We',
          min: 'Ср'
        },
        {
          label: 'четверг',
          value: 'Th',
          min: 'Чт'
        },
        {
          label: 'пятница',
          value: 'Fr',
          min: 'Пт'
        },
        {
          label: 'суббота',
          value: 'Sa',
          min: 'Сб'
        },
        {
          label: 'воскресение',
          value: 'Su',
          min: 'Вс'
        }
      ],
      weekValues: []
    }
  }),
  computed: {
    title() {
      const { start, end } = this
      // console.log(this)
      if (!start || !end) {
        return ''
      }
      switch (this.type) {
        case 'month':
          return this.$moment(start.date).format('MMMM YYYY')
        case 'week':
        case 'custom-weekly':
          return `${this.$moment(start.date).format('Do MMMM YYYY')} - ${this.$moment(end.date).format('Do MMMM YYYY')}`
        case '4day':
          return `${this.$moment(start.date).format('Do MMMM YYYY')} - ${this.$moment(end.date).format('Do MMMM YYYY')}`
        case 'day':
          return this.$moment(start.date).format('Do MMMM YYYY')
      }
      return ''
    },
    tooltipTitle() {
      return this.$moment().format('dddd, D MMMM YYYY')
    },
    filteredSpecializationsList() {
      const obj = this.specialization
      const newArray = []
      const serach = this.inputFiltr
      for (const key in obj) {
        const el = obj[key]
        if (el.label.toLowerCase().indexOf(serach) !== -1) newArray.push(el)
      }
      return newArray
    },
    filteredSpecialistList() {
      const obj = this.specialist
      const newArray = []
      const serach = this.inputFiltrD
      for (const key in obj) {
        const el = obj[key]
        if (el.label.toLowerCase().indexOf(serach) !== -1) newArray.push(el)
      }
      return newArray
    },
    addEventComputedDateFormatted() {
      return this.addEvent.date ? this.$moment(this.addEvent.date).format('Do MMMM YYYY') : ''
    },
    addEventComputedDateEndFormatted() {
      return this.addEvent.dateEnd ? this.$moment(this.addEvent.dateEnd).format('Do MMMM YYYY') : ''
    },
    icon() {
      if (this.chosenWeek) return 'mdi-close-box'
      if (this.chosenDey) return 'mdi-minus-box'
      return 'mdi-checkbox-blank-outline'
    },
    chosenWeek() {
      return this.addEvent.weekValues.length === this.addEvent.week.length
    },
    chosenDey() {
      return this.addEvent.weekValues.length > 0 && !this.chosenWeek
    }
  },
  watch: {
    selectedSpecialists(data) {
      setTimeout(() => {
        if (data.length !== 0) {
          this.getSpecializations(data, this.start.date, this.end.date)
        } else {
          this.events = []
        }
      }, 800)
    },
    selectedSpecializationsList(data) {
      setTimeout(() => {
        if (data.length !== 0) {
          this.getSpecialists(data, this.start.date, this.end.date)
        } else {
          this.events = []
        }
      }, 800)
    }
  },
  mounted() {
    this.$refs.calendar.checkChange()
    this.$vuetify.lang.current = 'ru'
    this.getEvent()
  },
  methods: {
    getSpecializations(data, startDate, endDate) {
      const startNamWeek = this.$moment(startDate).format('W')
      const endNamWeek = this.$moment(endDate).format('W')
      const dats = {
        // date_start: this.$moment(this.start.date).format('X'),
        date_start: this.$moment().isoWeek(startNamWeek).startOf('week').format('X'),
        // date_stop: this.$moment(this.end.date).add({ hours: 23, minutes: 59, seconds: 59 }).format('X'),
        date_stop: this.$moment().isoWeek(endNamWeek).startOf('week').add({ days: 6, hours: 23, minutes: 59, seconds: 59 }).format('X'),
        user_uid: data,
        specialization_uid: this.filtersEvent.specializationsModel
      }
      fetchScheduleSpecialists(dats)
        .then(response => {
          const zoneBrowser = this.$moment.tz.guess()
          if (response.status) {
            this.events = []
            for (const iterator of response.data.data) {
              if (Object.prototype.toString.call(iterator.date) === '[object Object]') {
                const color = colorHash.hex(iterator.name)
                for (const key in iterator.date) {
                  for (const item of iterator.date[key].data) {
                    this.events.push({
                      name: iterator.name,
                      specialization: `${iterator.name} (${item.specializationTitle})`,
                      details: `${iterator.timeZone} ${key} время приема с ${this.$moment.unix(item.time).tz(zoneBrowser).format('HH:mm')} до ${this.$moment.unix(item.time_stop).tz(zoneBrowser).format('HH:mm')}`,
                      start: this.$moment.unix(item.time).tz(zoneBrowser).format('YYYY-MM-DD HH:mm'),
                      end: this.$moment.unix(item.time_stop).tz(zoneBrowser).format('YYYY-MM-DD HH:mm'),
                      color: color
                    })
                  }
                }
              }
            }
          }
        })
    },
    getSpecialists(data, startDate, endDate) {
      const startNamWeek = this.$moment(this.start.date).format('W')
      const endNamWeek = this.$moment(this.end.date).format('W')
      const dats = {
        date_start: this.$moment().isoWeek(startNamWeek).startOf('week').format('X'),
        date_stop: this.$moment().isoWeek(endNamWeek).startOf('week').add({ days: 6, hours: 23, minutes: 59, seconds: 59 }).format('X'),
        user_uid: this.filtersEvent.specialistsModel,
        specialization_uid: data
      }
      fetchScheduleSpecialists(dats)
        .then(response => {
          // console.log(response)
          const zoneBrowser = this.$moment.tz.guess()
          if (response.status) {
            this.events = []
            for (const iterator of response.data.data) {
              if (Object.prototype.toString.call(iterator.date) === '[object Object]') {
                // console.log(iterator)
                const color = colorHash.hex(iterator.name)
                for (const key in iterator.date) {
                  // console.log(iterator.date[key].data)
                  for (const item of iterator.date[key].data) {
                    this.events.push({
                      name: iterator.name,
                      specialization: `${iterator.name} (${item.specializationTitle})`,
                      details: `${iterator.timeZone} ${key} время приема с ${this.$moment.unix(item.time).tz(zoneBrowser).format('HH:mm')} до ${this.$moment.unix(item.time_stop).tz(zoneBrowser).format('HH:mm')}`,
                      start: this.$moment.unix(item.time).tz(zoneBrowser).format('YYYY-MM-DD HH:mm'),
                      end: this.$moment.unix(item.time_stop).tz(zoneBrowser).format('YYYY-MM-DD HH:mm'),
                      color: color
                    })
                  }
                }
              }
            }
          }
        })
    },
    addEventTimeEnd() {
      const timeReception = this.addEvent.timeReception.split(':')
      const minutes = Number(timeReception[0]) * 60 + Number(timeReception[1])
      // console.log(minutes)
      return (this.addEvent.timeStart !== null) ? this.$moment(`${this.today} ${this.addEvent.timeStart}`).add(minutes, 'minutes').format('HH:mm') : null
    },
    rulesDateMenuStart() {
      if (this.addEvent.date) {
        return [true]
      } else if (this.addEvent.date === '' && this.addEvent.weekValues.length !== 0 && this.addEvent.radioGroup === 'radio2') {
        return [false || 'Выберете дату начала']
      } else if (this.addEvent.date === '' && this.addEvent.weekValues.length === 0 && this.addEvent.radioGroup === 'radio2') {
        return [false || 'Укажите день недели']
      }
    },
    choiceStartEndDay() {
      return this.$moment(this.addEvent.date).add(1, 'days').format('YYYY-MM-DD')
    },
    allowedDates(value) {
      for (const iterator of this.addEvent.weekValues) {
        if (this.$moment(value).locale('en').format('dd') === iterator) {
          return true
        }
      }
    },
    weekToggle() {
      this.$nextTick(() => {
        if (this.chosenWeek) {
          this.addEvent.weekValues = []
        } else {
          const selectAll = []
          for (const iterator of this.addEvent.week.slice()) {
            selectAll.push(iterator.value)
          }
          this.addEvent.weekValues = selectAll
        }
      })
    },
    removeWeeks(item) {
      const index = this.addEvent.weekValues.indexOf(item.value)
      if (index >= 0) this.addEvent.weekValues.splice(index, 1)
    },
    viewDay({ date }) {
      this.focus = date
      this.type = 'day'
    },
    getEventColor(event) {
      return event.color
    },
    setToday() {
      this.focus = this.today
    },
    prev() {
      this.$refs.calendar.prev()
    },
    showEvent({ nativeEvent, event }) {
      const open = () => {
        this.selectedEvent = event
        this.selectedElement = nativeEvent.target
        setTimeout(() => this.selectedOpen = true, 10)
      }
      if (this.selectedOpen) {
        this.selectedOpen = false
        setTimeout(open, 10)
      } else {
        open()
      }
      nativeEvent.stopPropagation()
    },
    updateRange({ start, end }) {
      this.start = start
      this.end = end
      if (this.selectedSpecialists.length !== 0) {
        this.getSpecializations(this.selectedSpecialists, this.start.date, this.end.date)
      } else if (this.selectedSpecializationsList.length !== 0) {
        this.getSpecialists(this.selectedSpecializationsList, this.start.date, this.end.date)
      } else {
        this.events = []
      }
    },
    next() {
      this.$refs.calendar.next()
    },
    clearCalendar() {
      this.picker = []
    },
    clickBarNav() {
      this.drawer = !this.drawer
      // this.picker = [new Date().toISOString().substr(0, 10)]
    },
    clickBarNav2(data) {
      if (this.drawer === true) this.drawer = false
      this.type = data.param
    },
    onFocus() {
      this.focused = true
    },
    onBlur() {
      this.focused = false
    },
    clickDate2(data) {
      this.type = 'custom-weekly'

      const startSort = this.$moment(data[0]).format('X')
      const endSort = this.$moment(data[1]).format('X')
      const arr = [startSort, endSort]
      const sort = arr.sort((a, b) => a - b)
      this.dataStart = this.$moment.unix(sort[0]).format('YYYY-MM-DD')
      this.dataEnd = this.$moment.unix(sort[1]).format('YYYY-MM-DD')
    },
    getEvent() {
      const data = {
        date_start: this.$moment(this.start.date).format('X'),
        date_stop: this.$moment(this.end.date).format('X')
      }
      fetchSpecialization()
        .then(response => {
          const specialization = []
          for (const key in response.data.specializations) {
            if (response.data.specializations.hasOwnProperty(key)) {
              specialization.push(
                {
                  value: key,
                  label: response.data.specializations[key].title

                })
            }
          }
          // console.log(specialization)
          this.specialization = specialization
          this.addEvent.specialization = specialization
        })

      getList()
        .then(response => {
          if (response.status) {
            // console.log(response)
            data.user_uid = Object.keys(response.data.data)
            // specialization_uid
            const specialist = []
            for (const key in response.data.data) {
              if (response.data.data.hasOwnProperty(key)) {
                const arr = []
                for (const iterator of response.data.data[key].specialization) {
                  arr.push(iterator.specialization_uid)
                }
                specialist.push(
                  {
                    value: key,
                    label: response.data.data[key].fullName,
                    color: colorHash.hex(response.data.data[key].fullName),
                    specialization: arr
                  })
                // const element = object[key];
              }
            }
            this.specialist = specialist
            this.addEvent.specialist = specialist
            // console.log(this.specialist)
          }
        })
    },
    changeSpecializations(data) {
      if (data.length !== 0) {
        this.rowWidthFiltr = '690px'
        this.rowWidthFiltrM = '465px'
        this.colsS = '6'
        this.filtersEvent.specializationsModel = data
      } else {
        this.rowWidthFiltr = ''
        this.rowWidthFiltrM = ''
        this.colsS = false
      }

      const spec = this.specialist
      const selectedSpecialists = []
      const specialists = []

      for (const iterator of spec) {
        // console.log(iterator)
        for (const i of data) {
          if (iterator.specialization.indexOf(i) !== -1) {
            // console.log(iterator.label)
            specialists.push(
              {
                value: iterator.value,
                label: iterator.label,
                color: iterator.color
              })
            selectedSpecialists.push(iterator.value)
          }
        }
      }

      this.filtersEvent.specialists = [...new Map(specialists.map(obj => [JSON.stringify(obj), obj])).values()]

      this.selectedSpecialists = []

      this.selectedSpecialists = [...new Set(selectedSpecialists)]

      if (data.length === 0) {
        this.selectedSpecialists = []
        this.events = []
      }
    },
    changeSpecialists(data) {
      if (data.length !== 0) {
        this.rowWidthFiltrD = '690px'
        this.rowWidthFiltrDM = '465px'
        this.colsD = '6'
        this.filtersEvent.specialistsModel = data
      } else {
        this.rowWidthFiltrD = ''
        this.rowWidthFiltrDM = ''
        this.colsD = false
      }

      const spec = this.specialist
      const specS = this.specialization
      const specializationId = []
      const specializationIdInput = []
      const specializationList = []

      for (const iterator of spec) {
        for (const i in data) {
          if (iterator.value.indexOf(data[i]) !== -1) {
            for (const iS in iterator.specialization) {
              specializationId.push(iterator.specialization[iS])
            }
          }
        }
      }

      const specializationIdList = [...new Set(specializationId)]

      for (const i of specS) {
        for (const id in specializationIdList) {
          if (i.value.indexOf(specializationIdList[id]) !== -1) {
            specializationList.push(
              {
                label: i.label,
                value: i.value
              }
            )
            specializationIdInput.push(i.value)
          }
        }
      }

      this.selectedSpecializationsList = [...new Set(specializationIdInput)]

      this.filtersEvent.specializations = [...new Map(specializationList.map(obj => [JSON.stringify(obj), obj])).values()]

    },
    addEventChangeSpecialization(data) {
      const spec = this.specialist
      const specialists = []

      if (data) {
        for (const iterator of spec) {
        // console.log(iterator)
        // for (const i of data) {
          if (iterator.specialization.indexOf(data) !== -1) {
            specialists.push(
              {
                value: iterator.value,
                label: iterator.label
              })
          }
        // }
        }
        // console.log(specialists)
        this.addEvent.specialist = specialists
      } else {
        this.addEvent.specialist = spec
      }
    },
    addEventChangeSpecialist(data) {
      const spec = this.specialization
      const specS = this.specialist
      const specializationUid = []
      const specialization = []
      if (data) {
        for (const iterator of specS) {
          if (iterator.value.indexOf(data) !== -1) {
            for (const i of iterator.specialization) {
              specializationUid.push(i)
            }
          }
        }

        for (const iterator of spec) {
          for (const i of specializationUid) {
            if (iterator.value.indexOf(i) !== -1) {
              specialization.push(
                {
                  value: iterator.value,
                  label: iterator.label
                })
            }
          }
        }
        this.addEvent.specialization = specialization
      } else {
        this.addEvent.specialization = spec
      }
    },
    getDate(startDate, stopDate, dayWeeks, startTime, endTime) {
      const dateArray = []
      const arrDate = []
      let currentDate = Number(this.$moment(startDate).format('X'))
      const stopDates = Number(this.$moment(stopDate).format('X'))
      while (currentDate <= stopDates) {
        dateArray.push(this.$moment.unix(currentDate).format('YYYY-MM-DD'))
        currentDate = Number(this.$moment.unix(currentDate).add(1, 'days').format('X'))
      }
      for (const key in dateArray) {
        for (const dayWeek in dayWeeks) {
          const dataDay = Number(this.$moment(dateArray[key]).format('X'))
          if (this.$moment.unix(dataDay).locale('en').format('dd') === dayWeeks[dayWeek]) {
            arrDate.push([this.$moment(`${dateArray[key]} ${startTime}`).format('X') - this.$moment().tz(this.$moment.tz.guess(true)).utcOffset() * 60, this.$moment(`${dateArray[key]} ${endTime}`).format('X') - this.$moment().tz(this.$moment.tz.guess(true)).utcOffset() * 60])
          }
        }
      }
      return arrDate
    },
    addEventSubmit() {
      if (this.$refs.form.validate()) {
        const timeReception = this.addEvent.timeReception.split(':')
        const minutes = Number(timeReception[0]) * 60 + Number(timeReception[1])
        this.snackbar = true
        const dateStart = this.$moment(`${this.addEvent.date} ${this.addEvent.timeStart}`).format('X') - this.$moment().tz(this.$moment.tz.guess(true)).utcOffset() * 60
        const dateEnd = this.$moment(`${this.addEvent.date} ${this.addEvent.timeEnd}`).format('X') - this.$moment().tz(this.$moment.tz.guess(true)).utcOffset() * 60
        const date = (this.addEvent.radioGroup === 'radio1')
          ? [dateStart, dateEnd]
          : (this.addEvent.radioGroup === 'radio2')
            ? this.getDate(this.addEvent.date, this.addEvent.dateEnd, this.addEvent.weekValues, this.addEvent.timeStart, this.addEvent.timeEnd)
            : []

        const data = {
          user_uid: this.addEvent.chosenSpecialist,
          specialization_uid: this.addEvent.chosenSpecialization,
          time_reception: minutes,
          date
        }

        addSchedule(data)
          .then(response => {
            // console.log(response)
            if (response.data.status) {
              this.addEvent.adSchedule = false
              this.addEventDumping()
              this.selectedSpecialists = []
              this.changeSpecialists(this.addEvent.chosenSpecialist)
              this.selectedSpecializationsList = this.addEvent.chosenSpecialization
            }
          })
      }
    },
    addEventDumping() {
      this.addEvent.adSchedule = false
      this.$refs.form.resetValidation()
      this.addEvent.chosenSpecialization = null
      this.addEvent.chosenSpecialist = null
      this.addEventChangeSpecialist()
      this.addEventChangeSpecialization()
      this.addEvent.timeStart = null
      this.addEvent.timeEnd = null
    },
    saveClinicInfo(data) {
      // console.log(data)
      this.dialogDelReasonContent.info = data
    }
  }
}
</script>

<style>
  .v-event-timed {
    min-width: 50px;
    /* margin-left: 100px; */
  }
  .v-application--wrap {
    min-height: 0;
  }
</style>
