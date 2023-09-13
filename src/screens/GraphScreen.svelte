<script>
  export let vehicleId;

  import { Line } from "svelte-chartjs";
  import { generateGraphData } from "../lib/data.js";

  import {
    Chart as ChartJS,
    Title,
    Tooltip,
    Legend,
    LineElement,
    LinearScale,
    PointElement,
    CategoryScale,
  } from "chart.js";

  ChartJS.register(
    Title,
    Tooltip,
    Legend,
    LineElement,
    LinearScale,
    PointElement,
    CategoryScale
  );

  let temperatureData = generateGraphData(vehicleId, [], []);
  let humidityData = generateGraphData(vehicleId, [], []);
  let doorData = generateGraphData(vehicleId, [], []);

  let temperatureWs = new WebSocket(
    `ws://${
      import.meta.env.VITE_API_DOMAIN
    }/vehicle/${vehicleId}/temperature/ws`
  );
  temperatureWs.onmessage = (event) => {
    let message = JSON.parse(event.data);

    temperatureData.labels.push(message.timestamp);
    temperatureData.datasets[0].data.push(message.value / 1000);

    temperatureData = generateGraphData(
      vehicleId,
      temperatureData.labels,
      temperatureData.datasets[0].data
    );
  };

  let humidityWs = new WebSocket(
    `ws://${import.meta.env.VITE_API_DOMAIN}/vehicle/${vehicleId}/humidity/ws`
  );
  humidityWs.onmessage = (event) => {
    let message = JSON.parse(event.data);

    humidityData.labels.push(message.timestamp);
    humidityData.datasets[0].data.push(message.value);

    humidityData = generateGraphData(
      vehicleId,
      humidityData.labels,
      humidityData.datasets[0].data
    );
  };

  let doorWs = new WebSocket(
    `ws://${import.meta.env.VITE_API_DOMAIN}/vehicle/${vehicleId}/door/ws`
  );
  doorWs.onmessage = (event) => {
    let message = JSON.parse(event.data);

    doorData.labels.push(message.timestamp);
    doorData.datasets[0].data.push(message.value ? 0 : 1);

    doorData = generateGraphData(
      vehicleId,
      doorData.labels,
      doorData.datasets[0].data
    );
  };
</script>

<ul role="list" class="space-y-3 mx-48 my-48">
  <li class="overflow-hidden bg-white px-4 py-4 shadow sm:rounded-md sm:px-6">
    Temperature (°C)
    <Line
      data={temperatureData}
      width={50}
      height={5}
      options={{ responsive: true, maintainAspectRatio: true }}
    />
  </li>
  <li class="overflow-hidden bg-white px-4 py-4 shadow sm:rounded-md sm:px-6">
    Humidity (%)
    <Line
      data={humidityData}
      width={50}
      height={5}
      options={{ responsive: true, maintainAspectRatio: true }}
    />
  </li>
  <li class="overflow-hidden bg-white px-4 py-4 shadow sm:rounded-md sm:px-6">
    Closed Door(True, False)
    <Line
      data={doorData}
      width={50}
      height={5}
      options={{ responsive: true, maintainAspectRatio: true }}
    />
  </li>
</ul>
