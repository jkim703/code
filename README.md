<!DOCTYPE html>
<html>

<!-- author: Seon Lee (fucema@gmail.com) -->
<!-- website: https://github.com/fucema/canvas-timer -->

<!-- 
MIT License
Copyright (c) 2016 Seon Lee (fucema@gmail.com)
Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:
The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.
THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
-->

<body style="background-color:#fff; margin:0; padding:0; font-family:Arial; color:#888;">

  <style media="screen" type="text/css">
    .grid:after {
      content: "";
      display: table;
      clear: both;
    }
    
    [class*="col-"] {
      float: left;
    }
    
    .col-2-3 {
      width: 66.66%;
    }
    
    .col-1-3 {
      width: 33.33%;
    }
    
    .today-pane {
      height: 200px;
      margin: 0;
      padding: 0;
    }
    
    .today-pane .dayofweek,
    .today-pane .datetime {
      padding: 0 30px;
    }
    .quarterly-pane {
      height: 200px;
      margin: 0;
      padding: 0;
    }
    .hourly-pane {
      height: 200px;
      margin: 0;
      padding: 0;
    }
    
    .quarterly-pane .description {
      text-align: center;
      font-weight: 300;
      font-size: 40px;
      word-break: break-word;
      margin: 0 0 30px;
      line-height: 68px;
      padding: 0 30px;
      cursor: default;
    }
    
    .ddhhmmss-pane .description {
      text-align: center;
      font-weight: 300;
      font-size: 40px;
      word-break: break-word;
      margin: 0 0 30px;
      line-height: 68px;
      padding: 0 30px;
      cursor: default;
    }
    
    .ddhhmmss-pane .description span.qualified,
    .ddhhmmss-pane .timer {
      font-size: 40px;
      font-weight: 300;
      text-align: center;
      margin-left: 30px;
      line-height: 46px;
      cursor: default;
    }
    
    .ddhhmmss-pane .timer span.days,
    .ddhhmmss-pane .timer span.hours,
    .ddhhmmss-pane .timer span.minutes,
    .ddhhmmss-pane .timer span.seconds,
    .ddhhmmss-pane .timer span.milliseconds {
      font-weight: 600;
      color: #666;
    }
    
    .ddhhmmss-pane .timer .seconds-container {
      min-width: 260px;
      font-weight: 300;
      text-align: left;
      display: inline-block;
    }
    
    .ddhhmmss-pane .timer .seconds-container span {
      font-weight: 600;
    }
    
    .ddhhmmss-pane .timer span.milliseconds {
      font-size: 0.5em;
      color: #ccc7c6;
      display: inline-block;
      text-align: left;
      width: 40px;
    }
  </style>

<script>
    // --------------------------------------------------
    // customizable settings
    // --------------------------------------------------
    // milliseconds between drawing, 1000 milliseconds = 1 second
    var draw_millis = 41;
    var quarters = [
      ["Q4", "December 31 2016 11:59:00 GMT-0400"],
    ];
    // --------------------------------------------------
    // do not modify anything below this line
    // --------------------------------------------------
    var weekdays = [
      ["Sunday"],
      ["Monday"],
      ["Tuesday"],
      ["Wednesday"],
      ["Thursday"],
      ["Friday"],
      ["Saturday"],
    ];
    var daydate = document.getElementsByClassName("today-pane")[0];
    var ddhhmmss = document.getElementsByClassName("ddhhmmss-pane")[0];
    var ddhhmmss_title = ddhhmmss.querySelector(".description");
    var daysSpan = ddhhmmss.querySelector("span.days");
    var hoursSpan = ddhhmmss.querySelector("span.hours");
    var minutesSpan = ddhhmmss.querySelector("span.minutes");
    var secondsSpan = ddhhmmss.querySelector("span.seconds");
    var millisecondsSpan = ddhhmmss.querySelector("span.milliseconds");
    var CurrentDayTime = {};
    var now = new Date();
    daydate.querySelector(".dayofweek").innerHTML = weekdays[now.getDay()];
    daydate.querySelector(".datetime").innerHTML = now.toTimeString();
    var ItsAlmost = {
      setNextPeriod: function() {
        var now = Date.now();
        for (i = 0; i < periods.length; i++) {
          var next = Date.parse(periods[i][1]);
          if (now <= next) {
            ddhhmmss_title.innerHTML = "until the end of " + periods[i][0];
            deadline = periods[i][1];
            break;
          }
        }
      },
      getTimeRemaining: function(enddatetime) {
        var t = Date.parse(enddatetime) - Date.now();
        var milliseconds = (t % 1000);
        var seconds = Math.floor((t / 1000) % 60);
        var minutes = Math.floor((t / 1000 / 60) % 60);
        var hours = Math.floor((t / (3600000)) % 24);
        var days = Math.floor(t / (86400000));
        return {
          "total": t,
          "days": days,
          "hours": hours,
          "minutes": minutes,
          "seconds": seconds,
          "milliseconds": milliseconds,
        }
      },
      update: function() {
        var t = ItsAlmost.getTimeRemaining(deadline);
        daysSpan.innerHTML = t.days;
        hoursSpan.innerHTML = t.hours;
        minutesSpan.innerHTML = t.minutes;
        secondsSpan.innerHTML = ("00" + t.seconds).slice(-2);
        millisecondsSpan.innerHTML = ("00" + t.milliseconds).slice(-3);
        if (t.total <= 0) {
          ItsAlmost.setNextPeriod();
        }
      },
    };
    ItsAlmost.setNextPeriod();
    ItsAlmost.update();
    setInterval(ItsAlmost.update, draw_millis);
  </script>


  <div class="grid">
    <div class="col-1-3 today-pane">
      <div class="running-pane">
        <div class="dayofweek description"></div>
        <div class="datetime description"></div>
      </div>
    </div>
    <div class="col-1-3 quarterly-pane">
      <div class="running-pane">
        <div class="description">
          Countdown until end of Q4       
        </div>
      </div>
    </div>
    <div class="col-1-3 hourly-pane">
      <div class="running-pane">
        <div class="timer">
          <canvas id="canvas" width="200" height="200">This browser does not support the HTML5 canvas tag.</canvas>
        </div>
      </div>
    </div>
  </div>

  <div class="ddhhmmss-pane">
    <div class="running-pane">
      <div class="timer">
        <span class="days"></span>&nbsp;days <span class="spacer"></span></span>
        <span class="hours"></span>&nbsp;hours <span class="spacer"></span></span>
        <span class="minutes"></span>&nbsp;minutes <span class="spacer"></span></span>
        <span class="seconds"></span>&nbsp;seconds <span class="spacer"></span></span>
        <span class="milliseconds"></span>&nbsp; <span class="spacer"></span></span>
      </div>
      <p class="description"></p>
    </div>
    <div class="finished-pane" style="display:none;"></div>
  </div>

  <script>
    // --------------------------------------------------
    // customizable settings
    // --------------------------------------------------
    // milliseconds between drawing, 1000 milliseconds = 1 second
    var draw_millis = 41;
    var periods = [
      ['SP-13', 'July 1 2016 20:00:00 GMT-0400'],
      ['SP-14', 'July 15 2016 20:00:00 GMT-0400'],
      ['SP-15', 'July 29 2016 20:00:00 GMT-0400'],
      ['SP-16', 'August 12 2016 20:00:00 GMT-0400'],
      ['SP-17', 'August 26 2016 20:00:00 GMT-0400'],
      ['SP-18', 'September 9 2016 20:00:00 GMT-0400'],
      ['SP-19', 'September 23 2016 20:00:00 GMT-0400'],
      ['SP-20', 'October 7 2016 20:00:00 GMT-0400'],
      ['SP-21', 'October 21 2016 20:00:00 GMT-0400'],
      ['SP-22', 'November 4 2016 20:00:00 GMT-0400'],
      ['SP-23', 'November 18 2016 20:00:00 GMT-0400'],
      ['SP-24', 'December 2 2016 20:00:00 GMT-0400'],
      ['SP-25', 'December 16 2016 20:00:00 GMT-0400'],
    ];
    var quarters = [
      ["Q4", "December 31 2016 11:59:00 GMT-0400"],
    ];
    // --------------------------------------------------
    // do not modify anything below this line
    // --------------------------------------------------
    var weekdays = [
      ["Sunday"],
      ["Monday"],
      ["Tuesday"],
      ["Wednesday"],
      ["Thursday"],
      ["Friday"],
      ["Saturday"],
    ];
    var daydate = document.getElementsByClassName("today-pane")[0];
    var ddhhmmss = document.getElementsByClassName("ddhhmmss-pane")[0];
    var ddhhmmss_title = ddhhmmss.querySelector(".description");
    var daysSpan = ddhhmmss.querySelector("span.days");
    var hoursSpan = ddhhmmss.querySelector("span.hours");
    var minutesSpan = ddhhmmss.querySelector("span.minutes");
    var secondsSpan = ddhhmmss.querySelector("span.seconds");
    var millisecondsSpan = ddhhmmss.querySelector("span.milliseconds");
    var CurrentDayTime = {};
    var now = new Date();
    daydate.querySelector(".dayofweek").innerHTML = weekdays[now.getDay()];
    daydate.querySelector(".datetime").innerHTML = now.toTimeString();
    var ItsAlmost = {
      setNextPeriod: function() {
        var now = Date.now();
        for (i = 0; i < periods.length; i++) {
          var next = Date.parse(periods[i][1]);
          if (now <= next) {
            ddhhmmss_title.innerHTML = "until the end of " + periods[i][0];
            deadline = periods[i][1];
            break;
          }
        }
      },
      getTimeRemaining: function(enddatetime) {
        var t = Date.parse(enddatetime) - Date.now();
        var milliseconds = (t % 1000);
        var seconds = Math.floor((t / 1000) % 60);
        var minutes = Math.floor((t / 1000 / 60) % 60);
        var hours = Math.floor((t / (3600000)) % 24);
        var days = Math.floor(t / (86400000));
        return {
          "total": t,
          "days": days,
          "hours": hours,
          "minutes": minutes,
          "seconds": seconds,
          "milliseconds": milliseconds,
        }
      },
      update: function() {
        var t = ItsAlmost.getTimeRemaining(deadline);
        daysSpan.innerHTML = t.days;
        hoursSpan.innerHTML = t.hours;
        minutesSpan.innerHTML = t.minutes;
        secondsSpan.innerHTML = ("00" + t.seconds).slice(-2);
        millisecondsSpan.innerHTML = ("00" + t.milliseconds).slice(-3);
        if (t.total <= 0) {
          ItsAlmost.setNextPeriod();
        }
      },
    };
    ItsAlmost.setNextPeriod();
    ItsAlmost.update();
    setInterval(ItsAlmost.update, draw_millis);
  </script>

  <script>
    // --------------------------------------------------
    // customizable settings
    // --------------------------------------------------
    // http://www.w3schools.com/colors/colors_picker.asp
    // if "true", displays the digits in a side-by-side layout
    var side_by_side = false;
    // color of the center of the timer behind the digits
    var timer_bg = "#ffffe6";
    var timer_scale = 0.9;
    // colors of the moving outer ring (remaining time, elapsed time)
    var remaining_bg = "#66ff33";
    var elapsed_bg = "#ff4d4d";
    var shadow_bg = "#a6a6a6";
    var ring_scale = 0.07;
    // font family name of the displayed digits
    var font_name = "arial";
    // stroke and fill colors of the displayed minutes digits
    var minutes_fg = "#fff";
    var minutes_bg = "#999";
    // stroke and fill colors of the displayed seconds digits
    var seconds_fg = "#fff";
    var seconds_bg = "#999";
    // --------------------------------------------------
    // do not modify anything below this line
    // --------------------------------------------------
    var ctx = document.getElementById("canvas").getContext("2d");
    var circ = Math.PI * 2;
    var quart = Math.PI / 2;
    var elapsed_scale = ring_scale;
    var radius = canvas.height / 2;
    ctx.translate(radius, radius);
    radius = radius * timer_scale;
    ring_scale = radius * ring_scale;
    elapsed_scale = radius * elapsed_scale;
    var HourlyTimer = {
      drawRemainingRing: function() {
        ctx.beginPath();
        // clear the canvas
        ctx.fillStyle = document.body.style.backgroundColor;
        ctx.fillRect(-canvas.width / 2, -canvas.height / 2, canvas.width, canvas.height);
        // clock face
        ctx.arc(0, 0, radius, 0, circ);
        ctx.lineWidth = ring_scale;
        ctx.fillStyle = timer_bg;
        ctx.fill();
        // minutes remaining ring
        ctx.lineWidth = ring_scale;
        ctx.strokeStyle = remaining_bg;
        ctx.stroke();
        // inner ring shadow
        ctx.beginPath();
        ctx.arc(0, 0, radius - (ring_scale * 0.5), 0, circ);
        ctx.lineWidth = 1;
        ctx.strokeStyle = shadow_bg;
        ctx.stroke();
      },
      drawElapsedRing: function(position) {
        // minutes elapsed ring
        ctx.beginPath();
        ctx.arc(0, 0, radius, -(quart), position);
        ctx.lineWidth = elapsed_scale;
        ctx.strokeStyle = elapsed_bg;
        ctx.stroke();
      },
      drawPositionDot: function(position) {
        // minutes position dot
        ctx.beginPath();
        ctx.rotate(-(quart) + position)
        ctx.arc(0, radius, elapsed_scale * 1.1, 0, circ);
        ctx.fillStyle = elapsed_bg;
        ctx.fill();
        ctx.rotate((quart) - position)
      },
      drawDigitsTopDown: function(remaining_minutes, remaining_seconds) {
        // digital minutes
        minutes_px = radius * 1.1;
        minutes_dx = 0;
        minutes_dy = minutes_px * 0.15;
        ctx.font = minutes_px + "px " + font_name;
        ctx.textBaseline = "alphabetic";
        ctx.textAlign = "center";
        ctx.strokeStyle = minutes_fg;
        ctx.lineWidth = radius * 0.02;
        ctx.strokeText(remaining_minutes, minutes_dx, minutes_dy);
        ctx.fillStyle = minutes_bg
        ctx.fillText(remaining_minutes, minutes_dx, minutes_dy);
        // digital seconds
        seconds_px = radius * 0.4;
        seconds_dx = seconds_px * 0.5;
        seconds_dy = radius * 0.7;
        ctx.font = seconds_px + "px " + font_name;
        ctx.textBaseline = "alphabetic";
        ctx.textAlign = "right";
        ctx.strokeStyle = seconds_fg;
        ctx.lineWidth = radius * 0.02;
        ctx.strokeText(remaining_seconds, seconds_dx, seconds_dy);
        ctx.fillStyle = seconds_bg
        ctx.fillText(remaining_seconds, seconds_dx, seconds_dy);
        // seconds "s" abbrev
        s_px = seconds_px * 0.5;
        s_dx = seconds_dx * 1.1;
        s_dy = seconds_dy;
        ctx.font = s_px + "px " + font_name;
        ctx.textAlign = "left";
        ctx.lineWidth = radius * 0.02;
        ctx.strokeText("s", s_dx, s_dy);
        ctx.fillStyle = seconds_bg
        ctx.fillText("s", s_dx, s_dy);
      },
      drawDigitsSideBySide: function(remaining_minutes, remaining_seconds) {
        // digital minutes
        minutes_px = radius * 1;
        minutes_dx = radius * 0.3;
        minutes_dy = radius * 0.4;
        ctx.font = minutes_px + "px " + font_name;
        ctx.textBaseline = "alphabetic";
        ctx.textAlign = "right";
        ctx.strokeStyle = minutes_fg;
        ctx.lineWidth = radius * 0.03;
        ctx.strokeText(remaining_minutes, minutes_dx, minutes_dy);
        ctx.fillStyle = minutes_bg
        ctx.fillText(remaining_minutes, minutes_dx, minutes_dy);
        // digital seconds
        seconds_px = radius * 0.4;
        seconds_dx = minutes_dx + 10;
        seconds_dy = minutes_dy;
        ctx.font = seconds_px + "px " + font_name;
        ctx.textBaseline = "alphabetic";
        ctx.textAlign = "left";
        ctx.strokeStyle = seconds_fg;
        ctx.lineWidth = radius * 0.03;
        ctx.strokeText(remaining_seconds, seconds_dx, seconds_dy);
        ctx.fillStyle = seconds_bg
        ctx.fillText(remaining_seconds, seconds_dx, seconds_dy);
      },
      draw: function() {
        var now = new Date();
        var minutes = now.getMinutes();
        var seconds = now.getSeconds();
        var pos = (((circ) * minutes / 60) - quart) + (seconds * Math.PI / (1800));
        HourlyTimer.drawRemainingRing();
        HourlyTimer.drawElapsedRing(pos);
        HourlyTimer.drawPositionDot(pos);
        var remaining_minutes = (59 - minutes).toString();
        var remaining_seconds = (59 - seconds).toString();
        if (side_by_side) {
          HourlyTimer.drawDigitsSideBySide(remaining_minutes, remaining_seconds);
        } else {
          HourlyTimer.drawDigitsTopDown(remaining_minutes, remaining_seconds);
        }
      }
    }
    HourlyTimer.draw();
    setInterval(HourlyTimer.draw, draw_millis);
  </script>

</body>

</html>
