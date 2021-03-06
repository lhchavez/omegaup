<template>
  <div>
    <div class="page-header">
      <h1>{{UI.formatString(T.contestEditWithTitle, {title: UI.contestTitle(contest)})}}
      <small><a v-bind:href=
      "`/arena/${contest.alias}/`">{{T.contestDetailsGoToContest}}</a></small></h1>
    </div>
    <ul class="nav nav-tabs nav-justified">
      <li v-bind:class="{active : !virtual}"
          v-if="!virtual"
          v-on:click="showTab = 'new_form'">
        <a data-toggle="tab">{{T.contestEdit}}</a>
      </li>
      <li class="problems"
          v-if="!virtual"
          v-on:click="showTab = 'problems'">
        <a data-toggle="tab">{{T.wordsAddProblem}}</a>
      </li>
      <li class="admission-mode"
          v-if="!virtual"
          v-on:click="showTab = 'publish'">
        <a data-toggle="tab">{{T.contestNewFormAdmissionMode}}</a>
      </li>
      <li class="contestants"
          v-bind:class="{active: virtual}"
          v-on:click="showTab = 'contestants'">
        <a data-toggle="tab">{{T.contestAdduserAddContestant}}</a>
      </li>
      <li v-on:click="showTab = 'admins'">
        <a data-toggle="tab">{{T.omegaupTitleContestAddAdmin}}</a>
      </li>
      <li v-if="!virtual"
          v-on:click="showTab = 'links'">
        <a data-toggle="tab">{{T.showLinks}}</a>
      </li>
      <li v-if="!virtual"
          v-on:click="showTab = 'clone'">
        <a data-toggle="tab">{{T.courseEditClone}}</a>
      </li>
    </ul>
    <div class="tab-content">
      <div class="tab-pane active"
           v-if="showTab === 'new_form'">
        <omegaup-contest-new-form v-bind:data="contest"
             v-bind:update="true"
             v-on:emit-update-contest=
             "newFormComponent =&gt; $emit('update-contest', newFormComponent)"></omegaup-contest-new-form>
      </div>
      <div class="tab-pane active problems"
           v-if="showTab === 'problems'">
        <omegaup-contest-add-problem v-bind:contest-alias="contest.alias"
             v-bind:data="problems"
             v-on:emit-add-problem=
             "addProblemComponent =&gt; $emit('add-problem', addProblemComponent)"
             v-on:emit-change-alias=
             "(addProblemComponent, newProblemAlias) =&gt; $emit('get-versions', newProblemAlias, addProblemComponent)"
             v-on:emit-remove-problem=
             "addProblemComponent =&gt; $emit('remove-problem', addProblemComponent)"
             v-on:emit-runs-diff=
             "(addProblemComponent, versions, selectedCommit) =&gt; $emit('runs-diff', addProblemComponent, versions, selectedCommit)">
        </omegaup-contest-add-problem>
      </div>
      <div class="tab-pane active"
           v-if="showTab === 'publish'">
        <omegaup-contest-publish v-bind:data="contest"
             v-on:emit-update-admission-mode=
             "publishComponent =&gt; $emit('update-admission-mode', publishComponent)"></omegaup-contest-publish>
      </div>
      <div class="tab-pane active contestants"
           v-if="showTab === 'contestants'">
        <omegaup-contest-contestant v-bind:contest="contest"
             v-bind:data="users"
             v-on:emit-add-user="contestantComponent =&gt; $emit('add-user', contestantComponent)"
             v-on:emit-remove-user=
             "contestantComponent =&gt; $emit('remove-user', contestantComponent)"
             v-on:emit-save-end-time=
             "selected =&gt; $emit('save-end-time', selected)"></omegaup-contest-contestant>
             <omegaup-contest-requests v-bind:data="requests"
             v-on:emit-accept-request=
             "(requestsComponent, username) =&gt; $emit('accept-request', requestsComponent, username)"
             v-on:emit-deny-request=
             "(requestsComponent, username) =&gt; $emit('deny-request', requestsComponent, username)"></omegaup-contest-requests>
      </div>
      <div class="tab-pane active"
           v-if="showTab === 'admins'">
        <omegaup-contest-admins v-bind:data="admins"
             v-on:emit-add-admin="addAdminComponent =&gt; $emit('add-admin', addAdminComponent)"
             v-on:emit-remove-admin=
             "addAdminComponent =&gt; $emit('remove-admin', addAdminComponent)"></omegaup-contest-admins>
             <omegaup-contest-group-admins v-bind:data="groupAdmins"
             v-on:emit-add-group-admin=
             "groupAdminsComponent =&gt; $emit('add-group-admin', groupAdminsComponent)"
             v-on:emit-remove-group-admin=
             "groupAdminsComponent =&gt; $emit('remove-group-admin', groupAdminsComponent)"></omegaup-contest-group-admins>
      </div>
      <div class="tab-pane active"
           v-if="showTab === 'links'">
        <omegaup-contest-links v-bind:data="contest"></omegaup-contest-links>
      </div>
      <div class="tab-pane active"
           v-if="showTab === 'clone'">
        <omegaup-contest-clone v-on:emit-clone=
        "cloneComponent =&gt; $emit('clone-contest', cloneComponent)"></omegaup-contest-clone>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { Vue, Component, Prop } from 'vue-property-decorator';
import { T } from '../../omegaup.js';
import UI from '../../ui.js';
import omegaup from '../../api.js';
import contest_AddProblem from './AddProblem.vue';
import contest_Admins from './Admins.vue';
import contest_Clone from './Clone.vue';
import contest_Contestant from './Contestant.vue';
import contest_Requests from './Requests.vue';
import contest_GroupAdmins from './GroupAdmins.vue';
import contest_Links from './Links.vue';
import contest_NewForm from './NewForm.vue';
import contest_Publish from './Publish.vue';

interface ContestEdit {
  admins: omegaup.ContestAdmin[];
  contest: omegaup.Contest;
  groupAdmins: omegaup.ContestGroupAdmin[];
  problems: omegaup.Problem[];
  requests: omegaup.IdentityContestRequest[];
  users: omegaup.IdentityContest[];
}

@Component({
  components: {
    'omegaup-contest-add-problem': contest_AddProblem,
    'omegaup-contest-admins': contest_Admins,
    'omegaup-contest-clone': contest_Clone,
    'omegaup-contest-contestant': contest_Contestant,
    'omegaup-contest-requests': contest_Requests,
    'omegaup-contest-group-admins': contest_GroupAdmins,
    'omegaup-contest-links': contest_Links,
    'omegaup-contest-new-form': contest_NewForm,
    'omegaup-contest-publish': contest_Publish,
  },
})
export default class Edit extends Vue {
  @Prop() data!: ContestEdit;

  T = T;
  UI = UI;
  showTab = UI.isVirtual(this.data.contest) ? 'contestants' : 'new_form';
  virtual = UI.isVirtual(this.data.contest);
  contest = this.data.contest;
  problems = this.data.problems;
  users = this.data.users;
  requests = this.data.requests;
  admins = this.data.admins;
  groupAdmins = this.data.groupAdmins;
}

</script>
