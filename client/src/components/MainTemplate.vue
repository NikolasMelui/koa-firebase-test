<template>
  <v-app id="inspire">
    <v-navigation-drawer
      fixed
      clipped
      class="grey lighten-4"
      app
      v-model="drawer"
    >
      <v-list
        dense
        class="grey lighten-4"
      >
        <template v-for="(item, i) in menuItems">
          <v-layout
            row
            v-if="item.heading"
            align-center
            :key="i"
          >
            <v-flex xs6>
              <v-subheader v-if="item.heading">
                {{ item.heading }}
              </v-subheader>
            </v-flex>
          </v-layout>
          <v-divider
            dark
            v-else-if="item.divider"
            class="my-3"
            :key="i"
          ></v-divider>
          <v-list-tile
            v-ripple
            :key="i"
            v-if="item.add"
            @click="eventAdderShow = true"
          >
          <v-dialog v-model="eventAdderShow" max-width="700">
              <v-card>
                <v-card-title align-center class="headline">Создать новое событие</v-card-title>
                <v-form ref="eventCreateForm" class="innerForm">
                  <v-text-field required v-model="eventTitleFormModel" prepend-icon="subtitles" name="eventTitle" label="Название проекта" type="text"></v-text-field>
                  <v-select
                    required
                    :items="eventTypesItems"
                    v-model="eventTypeFormModel"
                    label="Тип проекта:"
                    single-line
                    bottom
                  ></v-select>
                  <v-date-picker required align-center v-model="eventProcurementPlanDateFormModel" :landscape="landscape" :reactive="reactive"></v-date-picker>
                </v-form>

                <v-card-actions>
                  <v-spacer></v-spacer>
                  <v-btn color="black darken-1" flat="flat" @click.native="eventAdderShow = false">Отмена</v-btn>
                  <v-btn color="green darken-1" flat="flat" @click.native="eventAdderShow = false" @click="pushEvent">Добавить</v-btn>
                </v-card-actions>
              </v-card>
            </v-dialog>
            <v-list-tile-action>
              <v-icon>{{ item.icon }}</v-icon>
            </v-list-tile-action>
            <v-list-tile-content>
              <v-list-tile-title class="grey--text">
                {{ item.text }}
              </v-list-tile-title>
            </v-list-tile-content>
          </v-list-tile>
          <v-list-tile
            v-ripple
            :key="i"
            v-if="item.refresh"
            @click="getEvents"
          >
            <v-list-tile-action>
              <v-icon>{{ item.icon }}</v-icon>
            </v-list-tile-action>
            <v-list-tile-content>
              <v-list-tile-title class="grey--text">
                {{ item.text }}
              </v-list-tile-title>
            </v-list-tile-content>
          </v-list-tile>
          <v-list-tile
            v-ripple
            :key="i"
            v-if="item.archive"
            @click=""
          >
            <v-list-tile-action>
              <v-icon>{{ item.icon }}</v-icon>
            </v-list-tile-action>
            <v-list-tile-content>
              <v-list-tile-title class="grey--text">
                {{ item.text }}
              </v-list-tile-title>
            </v-list-tile-content>
          </v-list-tile>
          <v-list-tile
            v-ripple
            :key="i"
            v-if="item.help"
            @click=""
          >
            <v-list-tile-action>
              <v-icon>{{ item.icon }}</v-icon>
            </v-list-tile-action>
            <v-list-tile-content>
              <v-list-tile-title class="grey--text">
                {{ item.text }}
              </v-list-tile-title>
            </v-list-tile-content>
          </v-list-tile>
        </template>
      </v-list>
    </v-navigation-drawer>
    <v-toolbar color="amber" app absolute clipped-left>
      <v-toolbar-side-icon @click.native="drawer = !drawer"></v-toolbar-side-icon>
      <span class="title ml-3 mr-5">Сроки&nbsp;<span class="text">закупок</span></span>
      <v-text-field
        solo-inverted
        flat
        label="Поиск..."
        prepend-icon="search"
      ></v-text-field>
      <v-spacer></v-spacer>
    </v-toolbar>
    <v-content>
      <v-container fluid fill-height class="grey lighten-4">
        <v-layout justify-center align-center>
          <v-flex  shrink>
            <template v-for="(curEvent, i) in events">
              <v-card
              class="v-card"
              :key="i"
              >
                <v-card-title primary-title>
                  <div>
                    <h3 class="headline mb-0"> {{ curEvent.title }}</h3>
                    <div> {{ curEvent.type }} </div>
                    <div> {{ curEvent.procurementPlanDate}} </div>
                  </div>
                </v-card-title>
                <v-card-actions>
                  <v-btn flat color="orange">Изменить</v-btn>
                  <v-btn flat color="red">В архив</v-btn>
                </v-card-actions>
              </v-card>
            </template>
          </v-flex>
        </v-layout>
      </v-container>
    </v-content>
  </v-app>
</template>

<script>
import AuthenticationService from '@/services/AuthenticationService';
export default {
	name: 'MainTemplate',
	data: () => ({
		events: [],
		drawer: null,
		landscape: true,
		reactive: true,
		eventAdderShow: false,
		eventTitleFormModel: '',
		eventTypeFormModel: '',
		eventProcurementPlanDateFormModel: '',
		eventTypesItems: [
			{ text: 'Аукцион длинный' },
			{ text: 'Аукцион короткий' },
			{ text: 'Конкурс длинный' },
			{ text: 'Конкурс короткий' },
			{ text: 'Запрос котировок длинный' },
			{ text: 'Запрос котировок которкий' },
		],
		menuItems: [
			{ message: 'Привет' },
			{ heading: 'События' },
			{ icon: 'add', text: 'Добавить', add: true },
			{ icon: 'refresh', text: 'Обновить', refresh: true },
			{ divider: true },
			{ icon: 'archive', text: 'Архив', archive: true },
			{ divider: true },
			{ icon: 'help', text: 'Как это работает?', help: true },
		],
	}),
	props: {},
	methods: {
		async getEvents() {
			await AuthenticationService.getEvents()
				.then(res => (this.events = res.data))
				.catch(err => console.log(err));
		},
		async pushEvent() {
			await AuthenticationService.pushEvent({
				title: this.eventTitleFormModel,
				type: this.eventTypeFormModel,
				procurementPlanDate: this.eventProcurementPlanDateFormModel,
			})
				.then((req, res) => {
					this.eventTitleFormModel = '';
					this.eventTypeFormModel = '';
					this.eventDateFormModel = '';
					console.log(req.data);
					console.log(res.data);
				})
				.catch(err => console.log(err));
		},
	},
};
</script>

<style>
#keep main .container {
	height: 660px;
}
.navigation-drawer__border {
	display: none;
}
.text {
	font-weight: 400;
}
.v-card {
	margin-bottom: 20px;
}
.innerForm {
	width: 80%;
	margin: auto;
}
.picker__body {
	width: auto !important;
}
</style>