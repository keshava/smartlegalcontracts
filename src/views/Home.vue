<template>
  <div>
    <h1 v-if="loading">{{loading_text}}...</h1>
    <div class="max-w-sm rounded overflow-hidden shadow-lg bg-gray-500 text-white" v-else>
      <!--<img class="w-full" src="/img/card-top.jpg" alt="Sunset in the mountains">-->
      <div class="px-6 py-4">
        <div class="font-bold text-xl mb-2">Æcme Cooperative</div>
        <p class="text-white-700 text-base">
          <strong>Total members: </strong>{{contract_state.membership_map.length}}
        </p>
        <p class="text-white-700 text-base">
          <strong>Total shares: </strong>{{contract_state.total_shares}}
        </p>
        <p class="text-white-700 text-base">
          <strong>Total capital: </strong>{{capital}}
        </p>
        <p class="text-white-700 text-base">
          <strong>Application fee: </strong>{{contract_state.application_fee}}
        </p>
        <p class="text-white-700 text-base">
          <strong>Application shares: </strong>{{contract_state.member_s_initial_number_of_shares}}
        </p>
        <p class="text-white-700 text-base">
          <strong>Director: </strong>{{isDirector}}
        </p>
        <p class="text-white-700 text-base">
          <strong>Member: </strong>{{isMember}}
        </p>
        <p class="text-white-700 text-base" v-if="isMember">
          <strong>My shares: </strong>{{myShares}}
        </p>
        <p class="text-white-700 text-base" v-if="!isMember">
          <strong>Application pending: </strong>{{isApplicant}}
        </p>
        
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

          <button type="button" class="bg-purple-500 hover:bg-purple-700 text-white font-bold py-2 px-4 rounded" @click="return_all_shares">
            Return all shares
          </button>
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
  
  let contractAddress = "ct_M9aN7asfbWyt7RWDtsprYeFRidWhLW8DZiqonLxmd9r4ZTSGW";
  
  async function callStatic(func, args) {
    //Create a new contract instance that we can interact with
    const contract = await aeternity.client.getContractInstance(contractSource, {contractAddress});
    
    //Make a call to get data of smart contract func, with specefied arguments
    const calledGet = await contract.call(func, args, {callStatic: true}).catch(e => console.error("Static call error: " + e));
    
    //Make another call to decode the data received in first call
    const decodedGet = await calledGet.decode().catch(e => console.error("Decoding error: " + e));

    return decodedGet;
  }

  async function contractCall(func, args, value) {
    const contract = await aeternity.client.getContractInstance(contractSource, {contractAddress});
    
    //Make a call to write smart contract func, with aeon value input
    const calledSet = await contract.call(func, args, {amount: value}).catch(e => console.error("Contract call error: " + e));

    return calledSet;
  }

  export default {
    name: 'Home',
    components: {},
    data() {
      return {
        loading: true,
        loading_text: "Loading",
        contract_state: null,
        capital: 0,
        me: null,
        fldFee:0,
        fldShares:0,
      };
    },

    computed: {
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
    },

    async mounted() {
      this.loading_text = "Initializing client";
      await aeternity.initClient();
      this.loading_text = "Client initialized";
      // if (aeternity.isTestnet() && aeternity.balance <= 5) {
      //   this.loading_text = "Topping up"
      //   await axios.post(`https://testnet.faucet.aepps.com/account/${aeternity.address}`, {}, {headers: {'content-type': 'application/x-www-form-urlencoded'}}).catch(console.error);
      // }
      
      console.log("Address: " + aeternity.address)
      this.me = aeternity.address;

      this.loading_text = "Getting contract state"
      let state = await callStatic('get_state', []);
      console.log(state);
      this.contract_state = state;
      this.fldFee = state.application_fee;
      this.fldShares = state.member_s_initial_number_of_shares;

      this.loading_text = "Getting contract capital"
      let capital = await callStatic('capital', []);
      this.capital = capital;

      this.loading = false;
      this.loading_text = "Loading";
    },

    methods: {
      async get_capital() {
        this.contract_capital = await callStatic('capital', []);
      },

      async update_state() {
        this.state = await callStatic('get_state', []);
      },

      async set_fee() {
        console.log("set_fee clicked");
        let result = await contractCall('set_fee_and_applicant_shares', [this.fldFee, this.fldShares], 0);
        console.log(result);
        await this.update_state();
      },

      async apply() {
        console.log("apply clicked");
        let result = await contractCall('apply', [], this.contract_state.application_fee);
        console.log(result);
        await this.update_state();
      },

      async approve_membership(address) {
        console.log("approve_membership clicked");
        let result = await contractCall('accept_application', [address], 0);
        console.log(result);
        await this.update_state();
      },

      async reject_membership(address) {
        console.log("reject_membership clicked");
        let result = await contractCall('reject_application', [address], 0);
        console.log(result);
        await this.update_state();
      },

      async leave_membership() {
        console.log("leave_membership clicked");
        let result = await contractCall('leave_membership', [this.me], 0);
        console.log(result);
        await this.update_state();
      },

      async payout() {
        console.log("payout clicked");
        let result = await contractCall('payout', [this.me], 0);
        console.log(result);
        await this.update_state();
      },

      async return_all_shares() {
        console.log("return_all_shares clicked");
        let result = await contractCall('return_all_shares', [this.me], 0);
        console.log(result);
        await this.update_state();
      },
    }
  };
</script>

<style scoped>

</style>
