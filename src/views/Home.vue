<template>
  <div>
    <!-- Status messages -->
    <div class="bg-indigo-900 text-center py-4 lg:px-4" v-if="status.text.length > 0" @click="reset_status">
      <div class="p-2 bg-indigo-800 items-center text-indigo-100 leading-none lg:rounded-full flex lg:inline-flex" role="alert">
        <span class="flex rounded-full bg-indigo-500 uppercase px-2 py-1 text-xs font-bold mr-3" v-if="status.title.length > 0">{{status.title}}</span>
        <span class="font-semibold mr-2 text-left flex-auto">{{status.text}}</span>
        <span class="font-semibold mr-2 text-left flex-auto" v-if="status.url.length > 0"><a :href="status.url">View transaction</a></span>
        <svg class="fill-current opacity-5 h-4 w-4" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20"><path d="M12.95 10.707l.707-.707L8 4.343 6.586 5.757 10.828 10l-4.242 4.243L8 15.657l4.95-4.95z"/></svg>
      </div>
    </div>

    <!-- Loading messages -->
    <div class="bg-indigo-900 text-center py-4 lg:px-4" v-if="loading">
      <div class="p-2 bg-indigo-800 items-center text-indigo-100 leading-none lg:rounded-full flex lg:inline-flex" role="alert">
        <span class="flex rounded-full bg-indigo-500 uppercase px-2 py-1 text-xs font-bold mr-3">Busy</span>
        <span class="font-semibold mr-2 text-left flex-auto">{{loading_text}}...</span>
      </div>
    </div>

    <!-- Aepp -->
    <!-- <input class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-t@click="update_state" :disabled="fldContract.length < 52"ight focus:outline-none focus:shadow-outline" id="contract" type="text" placeholder="Cooperative's contract address" v-model="fldContract">
    <button type="button" class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded" @click="update_state" :disabled="fldContract.length < 52">
      Connect
    </button> -->
    <div class="px-6 py-4">
      <h1>Cooperætive's contract address</h1>
      <form class="w-full max-w-sm">
        <div class="flex items-center border-b border-b-2 border-purple-500 py-2">
          <input class="appearance-none bg-transparent border-none w-full text-gray-700 mr-3 py-1 px-2 leading-tight focus:outline-none" type="text" placeholder="Cooperætive's contract address" v-model="fldContract" aria-label="Contract address">
          <button class="flex-shrink-0 bg-purple-500 hover:bg-purple-700 border-purple-500 hover:border-purple-700 text-sm border-4 text-white py-1 px-2 rounded" type="button" @click="update_state" :disabled="fldContract.length < 52">
            Connect
          </button>
        </div>
      </form>

      <div v-if="contract_state == null">
        <h1>or register new cooperætive</h1>
        <form class="w-full max-w-lg">
          <div class="flex flex-wrap -mx-3 mb-2">
            <div class="w-full md:w-1/3 px-3 mb-6 md:mb-0">
              <label class="block uppercase tracking-wide text-gray-700 text-xs font-bold mb-2" for="grid-founder-shares">
                Founder shares
              </label>
              <input class="appearance-none block w-full bg-gray-200 text-gray-700 border border-gray-200 rounded py-3 px-4 leading-tight focus:outline-none focus:bg-white focus:border-gray-500" id="grid-founder-shares" type="number" step="1" placeholder="1000" v-model="fldFndShares">
            </div>
            <div class="w-full md:w-1/3 px-3 mb-6 md:mb-0">
              <label class="block uppercase tracking-wide text-gray-700 text-xs font-bold mb-2" for="grid-appl-fee">
                Application fee (in aettos)
              </label>
              <input class="appearance-none block w-full bg-gray-200 text-gray-700 border border-gray-200 rounded py-3 px-4 leading-tight focus:outline-none focus:bg-white focus:border-gray-500" id="grid-appl-fee" type="number" step="1" placeholder="1000" v-model="fldNewFee">
            </div>
            <div class="w-full md:w-1/3 px-3 mb-6 md:mb-0">
              <label class="block uppercase tracking-wide text-gray-700 text-xs font-bold mb-2" for="grid-appl-fee">
                Shares for fee
              </label>
              <input class="appearance-none block w-full bg-gray-200 text-gray-700 border border-gray-200 rounded py-3 px-4 leading-tight focus:outline-none focus:bg-white focus:border-gray-500" id="grid-appl-shares" type="number" step="1" placeholder="1000" v-model="fldNewShares">
            </div>
          </div>
          <button class="flex-shrink-0 bg-purple-500 hover:bg-purple-700 border-purple-500 hover:border-purple-700 text-sm border-4 text-white py-1 px-2 rounded" type="button" @click="deploy">
            Register
          </button>
        </form>
      </div>
    </div>

    <div class="max-w-sm rounded overflow-hidden shadow-lg bg-gray-500 text-white" v-if="!loading && contract_state != null">
      <!--<img class="w-full" src="/img/card-top.jpg" alt="Sunset in the mountains">-->
      <div class="px-6 py-4">
        <button type="button" class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded" @click="top_up" v-if="canTopUp">
          Fund account with 5 AE
        </button>
        <div class="font-bold text-xl mb-2">Cooperætive</div>
        <table>
          <tr>
            <th>Members</th>
            <td>{{contract_state.membership_map.length}}</td>
          </tr>
          <tr>
            <th>Shares</th>
            <td>{{contract_state.total_shares}}</td>
          </tr>
          <tr>
            <th>Capital</th>
            <td>{{contract_capital}} aettos</td>
          </tr>

          <tr>
            <th>Appl. fee</th>
            <td>{{contract_state.application_fee}} aettos for {{contract_state.member_s_initial_number_of_shares}} shares</td>
          </tr>

          <tr>
            <th>My roles</th>
            <td>
              <span v-if="isApplicant">Application pending</span>
              <span v-if="isMember">Member with {{myShares}} shares</span>
              <span v-if="isDirector">Director</span>
            </td>
          </tr>
        </table>
        
        <div v-if="!isMember && !isApplicant">
          <button type="button" class="bg-purple-500 hover:bg-purple-700 text-white font-bold py-2 px-4 rounded" @click="apply">
            Apply for membership
          </button>
        </div>
        <div v-if="isMember">
          <button type="button" class="bg-purple-500 hover:bg-purple-700 text-white font-bold py-2 px-4 rounded" @click="leave_membership">
            Leave membership
          </button>

          <button type="button" class="bg-purple-500 hover:bg-purple-700 text-white font-bold py-2 px-4 rounded" @click="payout">
            Payout
          </button>

          <!-- <button type="button" class="bg-purple-500 hover:bg-purple-700 text-white font-bold py-2 px-4 rounded" @click="return_all_shares">
            Return all shares
          </button> -->
        </div>
      </div>
      
      <div class="max-w-sm rounded overflow-hidden shadow-lg bg-gray-500 text-white" v-if="isDirector">
        <!--<img class="w-full" src="/img/card-top.jpg" alt="Sunset in the mountains">-->
        <div class="px-6 py-4">
          <div class="font-bold text-xl mb-2">Director operations</div>
          
          <h1>Set fee and applicant shares</h1>
          <form class="w-full max-w-sm" @submit.prevent="set_fee">
            <div class="md:flex md:items-center mb-6">
              <div class="md:w-1/3">
                <label class="block textwhite font-bold md:text-right mb-1 md:mb-0 pr-4" for="inline-full-name">
                  Application fee
                </label>
              </div>
              <div class="md:w-2/3">
                <input class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline" id="fee" type="number" step="1" placeholder="Fee" v-model="fldFee">
              </div>
            </div>
            <div class="md:flex md:items-center mb-6">
              <div class="md:w-1/3">
                <label class="block text-white font-bold md:text-right mb-1 md:mb-0 pr-4" for="inline-full-name">
                  Shares for fee
                </label>
              </div>
              <div class="md:w-2/3">
                <input class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline" id="shares" type="number" step="1" placeholder="Shares" v-model="fldShares">
              </div>
            </div>
            <button type="button" class="bg-purple-500 hover:bg-purple-700 text-white font-bold py-2 px-4 rounded" @click="set_fee">
              Set
            </button>
          </form>
          
          <div>
            <br><hr>
            <h1>Pending applications</h1>
            <table v-if="contract_state.application_map.length > 0">
              <tr>
                <th>Address</th>
                <th>Fee</th>
                <th>Action</th>
              </tr>

              <tr v-for="(application, i) in contract_state.application_map" :key="application[0]">
                <td>{{application[0].substring(0,10)}}</td>
                <td>{{application[1].fee}}</td>
                <td>
                  <button type="button" class="bg-purple-500 hover:bg-purple-700 text-white font-bold py-2 px-4 rounded" @click="approve_membership(application[0])">
                    Approve
                  </button>

                  <button type="button" class="bg-purple-500 hover:bg-purple-700 text-white font-bold py-2 px-4 rounded" @click="reject_membership(application[0])">
                    Reject
                  </button>
                </td>
              </tr>
            </table>
            <span v-else>No pending applications</span>
          </div>
        </div>
      </div>
    </div>
     
  </div>
</template>

<script>
  import aeternity from "../utils/aeternity";
  import axios from 'axios'

  const explorerUrl = 'https://testnet.explorer.aepps.com/#/tx/';

  const contractSource = `
  /* 21.a - coop charta snippet 1 (#3) */

  contract Coop =

    record state = { 
      founder : address,
      director : address,
      total_shares : int,
      application_fee : int,
      founder_s_initial_number_of_shares : int,
      member_s_initial_number_of_shares : int,
      application_map : map(address, application),
      membership_map : map(address, membership) }

    record application = { fee : int }

    record membership = {	owned_shares : int }

    public stateful entrypoint init(_founder_s_initial_number_of_shares : int, _application_fee : int, _member_s_initial_number_of_shares : int) =
      let founder = Call.caller 
      let founder_s_initial_number_of_shares = _founder_s_initial_number_of_shares
      let application_fee = _application_fee 
      let member_s_initial_number_of_shares = _member_s_initial_number_of_shares
      let director = founder
      { founder = founder,
        director = director,
        application_fee = _application_fee,
        member_s_initial_number_of_shares = _member_s_initial_number_of_shares,
        total_shares = _founder_s_initial_number_of_shares,
        founder_s_initial_number_of_shares = _founder_s_initial_number_of_shares,
        application_map = {},
        membership_map = { [founder] = {owned_shares=founder_s_initial_number_of_shares} } }

    public stateful entrypoint set_fee_and_applicant_shares(_application_fee : int, _member_s_initial_number_of_shares : int) =
      if(Call.caller == state.director) 
        put(state{ application_fee = _application_fee })
        put(state{ member_s_initial_number_of_shares = _member_s_initial_number_of_shares })

    public stateful entrypoint apply() =
      let applicant = Call.caller
      let fee = Call.value 
      create_application(applicant, fee)

    stateful entrypoint create_application(_applicant : address, _fee : int) =
      let applicant = _applicant
      let fee = _fee
      let application = { fee = fee }
      put(state{ application_map[applicant] = application })

    public stateful entrypoint reject_application(id : address) =
      if(Call.caller == state.director)
        Chain.spend(id, state.application_map[id].fee)
        put(state{ application_map = Map.delete(id, state.application_map) })

    public stateful entrypoint accept_application(id : address) =
      if(Call.caller == state.director)
        if(state.application_map[id].fee == state.application_fee && !Map.member(id, state.membership_map))
          create_membership(id, state.member_s_initial_number_of_shares) 
          put(state{ application_map = Map.delete(id, state.application_map) })

    stateful entrypoint create_membership(_member : address, _owned_shares : int) =
      let membership = { owned_shares = _owned_shares }
      put(state{ membership_map[_member] = membership })
      put(state{ total_shares = state.total_shares + state.member_s_initial_number_of_shares })

    public stateful entrypoint leave_membership(id : address) =
      Chain.spend(id, payout(id))
      return_all_shares(id)
      put(state{ membership_map = Map.delete(id, state.membership_map) })

    public stateful entrypoint payout(id : address) =
      Contract.balance / state.total_shares * state.membership_map[id].owned_shares

    public stateful entrypoint return_all_shares(id : address) =
      put(state{ total_shares = state.total_shares - state.membership_map[id].owned_shares})
      put(state{ membership_map[id].owned_shares = 0 })

    public entrypoint list_all_members() : map(address, membership) =
      if(Call.caller == state.director)
        state.membership_map
      else
        {}

    stateful entrypoint change_director(_director: address) = 
      if(Call.caller == state.director)
        put(state{ director = _director})
          
    entrypoint get_state() : state =
      state
      
    entrypoint capital() : int =
      Contract.balance`;
  
  let defaultContractAddress = "ct_Jjw4djSGpNrFunxxzakWBzbrDuyKBVafUTPqUcDC3mVKjUVNW";
  
 async function deployContract(founder_shares, membership_fee, membership_shares) {
   let compiled = await aeternity.client.contractCompile(contractSource).catch(e => console.error(e));;
   console.log(compiled);
   let deployed = await aeternity.client.contractDeploy(compiled.bytecode, contractSource, [`${founder_shares}`, `${membership_fee}`, `${membership_shares}`], {amount: membership_fee}).catch(e => console.error(e));
   console.log(deployed);
   
   return deployed;
 }
 
 //Create a asynchronous read call for our smart contract
async function callStatic(contractAddress, func, args) {
  //Create a new contract instance that we can interact with
  const contract = await aeternity.client.getContractInstance(contractSource, {contractAddress});
  //Make a call to get data of smart contract func, with specefied arguments
  const calledGet = await contract.call(func, args, {callStatic: true}).catch(e => console.error(e));
  //Make another call to decode the data received in first call
  const decodedGet = await calledGet.decode().catch(e => console.error(e));

  return decodedGet;
}

//Create a asynchronous write call for our smart contract
async function contractCall(contractAddress, func, args, value) {
  const contract = await aeternity.client.getContractInstance(contractSource, {contractAddress});
  //Make a call to write smart contract func, with aeon value input
  const calledSet = await contract.call(func, args, {amount: value}).catch(e => console.error(e));

  return calledSet;
}

  export default {
    name: 'Home',
    components: {},
    data() {
      return {
        loading: true,
        loading_text: "Loading",
        status: {
          title: "",
          text: "",
          url: "",
        },
        contract_state: null,
        contract_capital: 0,
        me: null,
        fldFee: 0,
        fldShares: 0,
        fldFndShares: 1000,
        fldNewFee: 1000,
        fldNewShares: 1000,
        fldContract: defaultContractAddress,
        contract_address: "",
      };
    },

    computed: {
      // contract_state() {
      //   return this._contract_state;
      // },

      isDirector() {
        return this.contract_state.director == this.me;
      },

      isMember() {
        for (var member of this.contract_state.membership_map) {
          if (member[0] == this.me) return true;
        }
        return false;
      },

      isApplicant() {
        for (var member of this.contract_state.application_map) {
          if (member[0] == this.me) return true;
        }
        return false;
      },

      myShares() {
        for (var member of this.contract_state.membership_map) {
          if (member[0] == this.me) return member[1].owned_shares;
        }
        return 0;
      },

      canTopUp() {
        return aeternity.isTestnet() && aeternity.balance <= 5
      },
    },

    async mounted() {
      this.loading_text = "Initializing client";
      await aeternity.initClient();
      this.loading_text = "Client initialized";
      
      console.log("Address: " + aeternity.address)
      this.me = aeternity.address;

      this.loading = false;
      this.loading_text = "Loading";
    },

    methods: {
      set_status(title, text, url) {
        this.status = {title, text, url};
        console.log(`[Status] title: ${title} text: ${text} url: ${url}`);
      },

      reset_status() {
        this.status = {title:"", text:"", url:""};
      },

      async top_up() {
        if (this.canTopUp) {
          this.loading_text = "Topping up";
          await axios.post(`https://testnet.faucet.aepps.com/account/${aeternity.address}`, {}, {headers: {'content-type': 'application/x-www-form-urlencoded'}}).catch(console.error);
        }
      },

      async deploy() {
        this.loading_text = "Deploying contract";
        this.loading = true;
        let deployed = await deployContract(this.fldFndShares, this.fldNewFee, this.fldNewShares);
        console.log("Finished deployment: " + deployed);
        if (deployed === undefined) {
          console.log("Error");
          this.set_status("Error", "Transaction failed. Please try again.", "");
        } else {
          console.log("Success");
          this.set_status("Success", "Successfully registered your cooperætive", `${explorerUrl}${deployed.transaction}`);
          this.contract_address = deployed.address;
          this.fldContract = deployed.address;
          // this.loading_text = "Waiting for transaction to process";
          // sleep(10000);
          this.loading_text = "Getting contract state";
          await this.update_state();
        }

        this.loading = false;
      },

      async update_state() {
        this.contract_address = this.fldContract;
        this.loading_text = "Getting contract state";
        this.contract_state = await callStatic(this.contract_address, 'get_state', []);
        this.contract_capital = await callStatic(this.contract_address, 'capital', []);
        this.fldFee = this.contract_state.application_fee;
        this.fldShares = this.contract_state.member_s_initial_number_of_shares;
      },

      async set_fee() {
        console.log("set_fee clicked");
        this.loading_text="Sending transaction to network";
        this.loading = true;
        let result = await contractCall(this.contract_address, 'set_fee_and_applicant_shares', [this.fldFee, this.fldShares], 0);
        if (result === undefined) {
          this.set_status("Error", "Transaction failed. Please try again.", "");
        } else {
          this.set_status("Success", "Successfully updated fees", `${explorerUrl}${result.hash}`);
          await this.update_state();
        }
        this.loading = false;       
      },

      async apply() {
        console.log("apply clicked");
        this.loading_text="Sending transaction to network";
        this.loading = true;
        let result = await contractCall(this.contract_address, 'apply', [], this.contract_state.application_fee);
        if (result === undefined) {
          this.set_status("Error", "Transaction failed. Please try again.", "");
        } else {
          this.set_status("Success", "Successfully applied for membership", `${explorerUrl}${result.hash}`);
          await this.update_state();
        }
        this.loading = false;
      },

      async approve_membership(address) {
        console.log("approve_membership clicked");
        this.loading_text="Sending transaction to network";
        this.loading = true;
        let result = await contractCall(this.contract_address, 'accept_application', [address], 0);
        if (result === undefined) {
          this.set_status("Error", "Transaction failed. Please try again.", "");
        } else {
          this.set_status("Success", "Successfully approved membership", `${explorerUrl}${result.hash}`);
          await this.update_state();
        }
        this.loading = false;
      },

      async reject_membership(address) {
        console.log("reject_membership clicked");
        this.loading_text="Sending transaction to network";
        this.loading = true;
        let result = await contractCall(this.contract_address, 'reject_application', [address], 0);
        if (result === undefined) {
          this.set_status("Error", "Transaction failed. Please try again.", "");
        } else {
          this.set_status("Success", "Successfully rejected membership", `${explorerUrl}${result.hash}`);
          await this.update_state();
        } 
        this.loading = false;
      },

      async leave_membership() {
        console.log("leave_membership clicked");
        this.loading_text="Sending transaction to network";
        this.loading = true;
        let result = await contractCall(this.contract_address, 'leave_membership', [this.me], 0);
        if (result === undefined) {
          this.set_status("Error", "Transaction failed. Please try again.", "");
        } else {
          this.set_status("Success", "Successfully surrendered membership", `${explorerUrl}${result.hash}`);
          await this.update_state();
        } 
        this.loading = false;
      },

      async payout() {
        console.log("payout clicked");
        this.loading_text="Sending transaction to network";
        this.loading = true;
        let result = await contractCall(this.contract_address, 'payout', [this.me], 0);
        if (result === undefined) {
          this.set_status("Error", "Transaction failed. Please try again.", "");
        } else {
          this.set_status("Success", "Successfully paid out", `${explorerUrl}${result.hash}`);
          await this.update_state();
        }
        this.loading = false;
      },

      // async return_all_shares() {
      //   console.log("return_all_shares clicked");
      //   let result = await contractCall('return_all_shares', [this.me], 0);
      //   console.log(result);
      //   await this.update_state();
      // },
    }
  };
</script>

<style scoped>

</style>
