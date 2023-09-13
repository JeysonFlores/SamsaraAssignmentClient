<script>
  import WelcomeScreen from "./screens/WelcomeScreen.svelte";
  import GraphScreen from "./screens/GraphScreen.svelte";

  let currentVehicle;

  const setVehicleId = async (vehicleId) => {
    currentVehicle = vehicleId;
  };
</script>




{#if currentVehicle}
  <GraphScreen vehicleId={currentVehicle}/>
{:else}
  <WelcomeScreen action={setVehicleId}/>
{/if}