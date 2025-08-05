<script>
  const notification = '/notificationTone.mp3';
  
  let intervals = 1;

  // Task management
let tasks = [];
let taskInput = '';

function addTask() {
  if (taskInput.trim() !== '') {
    tasks = [
      ...tasks,
      { id: Date.now() + Math.random(), text: taskInput.trim(), completed: false }
    ];
    taskInput = '';
  }
}

function toggleTask(id) {
  tasks = tasks.map(task =>
    task.id === id ? { ...task, completed: !task.completed } : task
  );
}

  function playNotification() {
    const audio = new Audio(notification);
    audio.play();
  }

  let timerStatus = '';
  let timeLeft = 0;
  let timerInterval = null;

  function formatTime(ms = 0) {
    const totalSeconds = Math.floor(ms / 1000);
    const minutes = Math.floor(totalSeconds / 60);
    const seconds = totalSeconds % 60;
    return `${minutes.toString().padStart(2, '0')}:${seconds.toString().padStart(2, '0')}`;
  }

  async function startTimer(numberOfIntervals = 1) {
    const interval1Duration = 25 * 60 * 1000; // 25 minutes in ms
    const interval2Duration = 5 * 60 * 1000;  // 5 minutes in ms
    let currentInterval = 0;
    let isWork = true;

    function sleep(ms) {
      return new Promise(resolve => setTimeout(resolve, ms));
    }

    for (currentInterval = 0; currentInterval < numberOfIntervals * 2; currentInterval++) {
      isWork = currentInterval % 2 === 0;
      const duration = isWork ? interval1Duration : interval2Duration;
      timeLeft = duration;
      timerStatus = isWork
        ? `Interval ${Math.floor(currentInterval / 2) + 1}: Work`
        : `Interval ${Math.floor(currentInterval / 2) + 1}: Break`;
      playNotification();

      while (timeLeft > 0) {
        await sleep(1000);
        timeLeft -= 1000;
      }
    }
    timerStatus = 'All intervals completed!';
    timeLeft = 0;
  }
</script>

<main>
  <h1>Welcome to focustimerPRO</h1>
  <form>
    <label for="intervals">Number of Intervals:</label>
    <input type="number" id="intervals" name="intervals" min="1" max="10" bind:value={intervals}>
    <br>
    <button type="button" on:click={() => startTimer(intervals)}>Start Timer</button>
  </form>
  <div>
    <h2>{timerStatus}</h2>
    <p>Time Left: {formatTime(timeLeft)}</p>
  </div>
  <form on:submit|preventDefault={addTask}>
    <label for="tasks">Task input:</label>
    <input type="text" id="tasks" name="tasks" placeholder="Enter your tasks here..." bind:value={taskInput}>
    <button type="submit">Add Task</button>
  </form>
  <ul style="list-style: none; padding: 0;">
    {#each tasks as task (task.id)}
      <li>
        <label style="display: flex; align-items: center; gap: 0.5em;">
          <input type="checkbox" checked={task.completed} on:change={() => toggleTask(task.id)}>
          <span style={task.completed ? 'text-decoration: line-through; color: #888;' : ''}>{task.text}</span>
        </label>
      </li>
    {/each}
  </ul>
</main>
<style>
  
</style>
