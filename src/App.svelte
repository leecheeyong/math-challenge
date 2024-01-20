<script>
  import { onMount } from 'svelte';
  var time;
  var question;
  let stopwatch = "";
  let watch;
  var answer;
  var status = "Solve me !!";
  var ans;
  var streak = 0;
  var pastQuestions = [];
  var stopped = false;
  const operators = {
      "x": "*",
      "+": "+",
      "-": "-"
  }
  function focus(el) { el.focus(); }
  $: if(ans) ans.onchange = (e) => {
      if(e.currentTarget.value == answer) {
          stopCount(); setTimeout(() => { startCount(); status = "Solve me !" }, 1500);
          pastQuestions.push(time);
          status = "Correct !"
          streak+= 1;
      }else {
          status = "Wrong"
          setTimeout(() => { status = "Solve me !" }, 1500);
      }
  }
  const generate = () => {
      const rand = (e) => Math.floor(Math.random() * e);
      var result;
      result = [];
      for(let i=0; i<(rand(2)+1); i++) {
      const opt = Object.keys(operators)[rand(Object.keys(operators).length)];
      const operator = operators[opt];
      result.push({ opt, operator })
      }
      const com = result.map(e => `${rand(9)+1}${e.operator}${rand(9)+1}`).join("");
      question = com;
      //answer = eval(com);
      answer = new Function("return "+ com)();
  }
  let startCount = (e) => {
  if(e) e.currentTarget.style.display = "none";
  time = 0;
  stopped = false;
  generate()
  watch =  setInterval(() => {
  var minutes = Math.floor((time % (1000 * 60 * 60)) / (1000 * 60));
  var seconds = Math.floor((time % (1000 * 60)) / 1000);
  time+=1000;
  stopwatch = `${minutes ? `${minutes}min` : ""} ${seconds}sec`
  }, 1000);
  if(ans) ans?.focus();
  }
  let stopCount = (e) => {
      if(e && e.currentTarget.textContent != "Stop") {
          e.currentTarget.textContent = "Stop";
          pastQuestions = [];
          streak = 0;
          startCount()
      }else {
          if(e) e.currentTarget.textContent = "Another Round";
          stopped = true;
          clearInterval(watch);
      }
  }
  </script>
  <div class="score">
    {#if time}
    <p>{streak}&numsp;<span class="span">Streak</span></p>
    {:else}
      <p><a href="https://github.com/leecheeyong/math-challenge" style="font-family: 'Franklin Gothic Medium', 'Arial Narrow', Arial, sans-serif" target="_blank" rel="noreferrer">Math Challenge</a></p>
    {/if}
    </div>
  <div class="container">
  <button on:click={startCount}>Start</button>
  {#if time}
  {#if stopped}
    <p>Result</p>
    <h2 style="margin-bottom:10px; font-family:Arial, Helvetica, sans-serif;">Streak: {streak}<br>QPS: {(pastQuestions.reduce((sum, a) => sum + a, 0) / 1000 / pastQuestions.length).toFixed(0) }</h2>
  {:else}
  <p>{stopwatch}</p>
  <h1>{question}</h1>
  <p>{status}</p>
  <input bind:this={ans} type="number" use:focus/>
    {/if}
    <button on:click={stopCount}>Stop</button>
  {/if}
  </div>
  <div class="footer">
      <p><a href="https://github.com/leecheeyong/math-challenge" target="_blank" rel="noreferrer">Math Game</a> &copy; Lee Chee Yong 2024</p>
      <p><a href="https://github.com/leecheeyong/math-challenge" target="_blank" rel="noreferrer">This project</a> is available as open source under the terms of the <a href="https://github.com/leecheeyong/math-game/LICENSE">MIT License</a></p>
  </div>
  