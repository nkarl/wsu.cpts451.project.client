<!-- App GUI -->
<script>
  import { onMount, onDestroy } from 'svelte';
  import axios from 'axios';
  // export let data = [{}, {}];

  //const endpoint_req = 'http://localhost:3000/v1/get';
  const endpoint_req = 'http://localhost:3000/v1/test';
  $: states = [{}];
  $: cities = [{}];
  $: zipcodes = [{}];
  $: businesses = [{}];
  $: state_current = '';
  $: city_current = '';
  $: zipcode_current = '';

  const res = {};
  // resfresh your_end_point_prop
  export async function load(fetch, params) {
    const res = await fetch(endpoint_req + `${params}`);
    const item = await res.json();
    return { item };
  }

  const getStates = async () => {
    try {
      /* get states */
      res.states = await axios.get(endpoint_req);
      states = res.states.data;
      //res.states = await fetch(endpoint_req);
      //states = await res.states.json();
      //states = load(fetch, '').then(states => states);
      console.log(states);
    } catch (error) {
      console.log('THERE IS AN ERROR.');
      console.error(error);
    } finally {
      console.log('successful fetched data.');
    }
  };

  const getCities = async (state = 'PA') => {
    state_current = state;
    try {
      /* get states */
      res.cities = await axios.get(`${endpoint_req}?state=${state}`);
      cities = res.cities.data;
      //res.cities = await fetch(endpoint_req + '?state=' + state);
      //cities = await res.cities.json();
      console.log('state_current=', state_current);
      console.log(cities);
    } catch (error) {
      console.log('THERE IS AN ERROR.');
      console.error(error);
    } finally {
      console.log('successful fetched data.');
    }
  };

  const getZipcodes = async (city = 'Pittsburgh') => {
    city_current = city;
    try {
      /* get states */
      res.zipcodes = await axios.get(`${endpoint_req}?state=${state_current}&city=${city}`);
      zipcodes = res.zipcodes.data;
      //res.businesses = await fetch(endpoint_req + '?state=' + state_current + '&' + 'city=' + city);
      //businesses = await res.businesses.json();
      console.log(zipcodes);
    } catch (error) {
      console.log('THERE IS AN ERROR.');
      console.error(error);
    } finally {
      console.log('successful fetched data.');
    }
  };

  const getBusinesses = async (zipcode = '15201') => {
    zipcode_current = zipcode;
    try {
      /* get states */
      res.businesses = await axios.get(
        `${endpoint_req}?state=${state_current}&city=${city_current}&zipcode=${zipcode}`
      );
      businesses = res.businesses.data;
      //res.businesses = await fetch(endpoint_req + '?state=' + state_current + '&' + 'city=' + city);
      //businesses = await res.businesses.json();
      console.log(businesses);
    } catch (error) {
      console.log('THERE IS AN ERROR.');
      console.error(error);
    } finally {
      console.log('successful fetched data.');
    }
  };

  onMount(async function () {
    /*
            // Default fetch API
			const res = await fetch(endpoint);
			const data = await res.json();
			posts = data;
        */
    state_current = 'PA';
    city_current = 'Pittsburgh';
    zipcode_current = '15201';
    getStates();
    getCities();
    getZipcodes();
    getBusinesses();
  });
</script>

<!-- App GUI -->
<div class="app-container">
  <!-- LEFT PANEL: CITY LIST PER SELECTED STATE -->
  <div class="panel" id="left-panel">
    {#if state_current}
      <div>
        <h3>State List DropDown</h3>
        <select id="state-list" name="">
          {#await getStates() then}
            {#each states as s, index (index)}
              <option id="s{index + 1}" on:click={() => getCities(s.state)}>{s.state}</option>
            {/each}
          {/await}
        </select>
      </div>
      <div class="in-state-panel">
        <!-- City List -->
        <div class="in-state-component">
          <h3>City List</h3>
          <select size="5" id="in-state-component-list">
            {#await getCities() then}
              {#each cities as c, index (index)}
                {#if c.city === city_current}
                  <option
                    id="s{index + 1}"
                    value={c.city}
                    on:click={() => getZipcodes(c.city)}
                    selected>{c.city}</option>
                {:else}
                  <option id="s{index + 1}" value={c.city} on:click={() => getZipcodes(c.city)}
                    >{c.city}</option>
                {/if}
              {/each}
            {/await}
          </select>
        </div>

        <!-- Zipcode List -->
        <div class="in-state-component">
          <h3>Zipcode List</h3>
          <select size="5" id="in-state-component-list">
            {#await getZipcodes() then}
              {#each zipcodes as z, index (index)}
                {#if z.zipcode === zipcode_current}
                  <option
                    id="s{index + 1}"
                    value={z.zipcode}
                    on:click={() => getBusinesses(z.zipcode)}
                    selected>{z.zipcode}</option>
                {:else}
                  <option
                    id="s{index + 1}"
                    value={z.zipcode}
                    on:click={() => getBusinesses(z.zipcode)}>{z.zipcode}</option>
                {/if}
              {/each}
            {/await}
          </select>
        </div>
      </div>
    {/if}
  </div>

  <!-- RIGHT PANEL: BUSINESS LIST PER SELECTED CITY -->
  <div class="panel" style="width:75%">
    <h3>Business List</h3>
    <!--<select size="5" id="data-option-panel">-->
    <div class="scrollable">
      <table>
        <tr>
          <th class="table-header-row inner-column">Name</th>
          <th class="table-header-row inner-column">Address</th>
          <!--<th class="table-header-row inner-column">Zipcode</th>-->
          <th class="table-header-row inner-column">Rating</th>
          <th class="table-header-row inner-column">Reviews</th>
          <th class="table-header-row">Checkins</th>
        </tr>
        {#each businesses as b, index (index)}
          <tr>
            <td class="inner-column" style="width:12%">{b.name}</td>
            <td class="inner-column" style="width:12%">{b.address}</td>
            <!--<td class="inner-column">{b.zipcode}</td>-->
            <td class="inner-column">{b.stars}</td>
            <td class="inner-column">{b.num_reviews}</td>
            <td>{b.num_checkins}</td>
          </tr>
        {/each}
      </table>
    </div>
    <!--</select>-->
  </div>

  <div id="ribbon">
    <div class="share">
      <i class="fa fa-share-square-o" />
      <div id="easter-egg" />
    </div>
    <div style="position:unset;display:flex;margin-left:15%;justify-content: space-evenly;">
      <div class="in-state-component ribbon-list">
        <h3>Popular List</h3>
        <select size="5" id="in-state-component-list" />
      </div>
      <div class="in-state-component ribbon-list">
        <h3>Successful List</h3>
        <select size="5" id="in-state-component-list" />
      </div>
    </div>
  </div>
</div>

<!-- Style Sheet -->
<style>
  .app-container {
    /*text-align: center;*/
    width: 95%;
    min-height: 95%;
    display: flex;
    margin-left: 2%;
    margin-right: 2%;
    font-size: 18px;
  }

  .panel {
    border: 5px solid gray;
    width: 1000px;
    min-height: 95%;
    margin: 5px 5px;
    padding: 2% 2% 2% 2%;
    flex-direction: row wrap;
    border-radius: 25px;
    font-size: 15px;
  }

  div > #state-list {
    width: 95%;
    height: 50px;
    margin-left: 1%;
    margin-right: 1%;
    margin-bottom: 4%;
    font-size: 1rm;
  }

  .in-state-panel {
    display: unset;
    display: flex;
    justify-content: space-evenly;
    flex-direction: unset;
    flex-direction: row wrap;
  }

  #in-state-component-list {
    width: 225px;
    min-height: 300px;
    margin-left: unset;
    margin-bottom: 4%;
    font-size: 1rm;
  }

  #left-panel {
    width: unset;
    width: 40%;
    font-size: 15px;
    padding: unset;
    padding: 2% 2%;
  }

  select {
    border-radius: 10px;
    font-size: 1em;
  }

  th,
  td {
    text-align: left;
    width: 0.75%;
    border-bottom: solid black 1px;
    padding: 0 1% 0 1%;
    font-size: 15px;
  }

  .table-header-row {
    border-bottom: solid black 1px;
  }

  .inner-column {
    border-right: solid black 1px;
  }

  .scrollable {
    overflow-y: scroll;
    height: 72vh;
  }

  #ribbon {
    border: 5px solid gray;
    width: 200px;
    height: 200px;
    background-color: white;
    position: fixed;
    /*left: 100px;*/
    /*bottom: -150px;*/
    right: -230px;
    transition: 0.5s ease;
    box-shadow: -5px 5px 15px black;
    border-radius: 25px;
    display: unset;
    padding: 2%;
  }

  .ribbon-list {
    margin: unset;
    margin-top: 10%;
    padding: unset;
    font-size: 16px;
  }

  #easter-egg {
    right: -350px;
    height: 250px;
    width: 250px;
    /*margin-left: 100px;
		margin-top: 100px;*/
    background-image: url('../../assets/cat-engineer.gif');
    background-repeat: no-repeat;
    background-size: 100%;
    opacity: 0.75;
    position: absolute;
  }

  #ribbon:hover {
    height: 75%;
    width: 75%;
    /*padding: 50px;*/
  }

  .share {
    font-size: 3em;
    float: top;
    position: relative;
    top: 35px;
  }
</style>
