<script>
    import supabase from "$lib/db";
    import { onMount } from "svelte";
    import { user } from "$lib/stores";
    import { goto } from "$app/navigation";

    let attends = [];
  
  
    onMount(async () => {
      const studId = 13;
  
      let { data, error } = await supabase
        .from("Attendance")
        .select("*")
        .eq("Stud_Id", studId);
  
      if (error) {
        console.log(error);
      } else {
      
        const groupedData = data.reduce((acc, curr) => {
          const periodIndex = curr.period - 1;
          if (acc[curr.date]) {
            acc[curr.date].periods[periodIndex] = {
              period: curr.period,
              status: curr.status,
            };
          } else {
            acc[curr.date] = {
              date: curr.date,
              periods: [
                { period: 1, status: false },
                { period: 2, status: false },
                { period: 3, status: false },
                { period: 4, status: false },
                { period: 5, status: false },
                { period: 6, status: false },
                { period: 7, status: false },
              ],
            };
            acc[curr.date].periods[periodIndex] = {
              period: curr.period,
              status: curr.status,
            };
          }
          return acc;
        }, {});
  
        attends = Object.values(groupedData);
        console.table(attends);
      }
    });
  
    async function sendData1() {
    const date = document.getElementById("date-input").value;
    const period = document.getElementById("period-input").value;
    const present = document.getElementById("present-checkbox").checked;
  
    const { data: attendanceData, error: attendanceError } = await supabase
      .from("Attendance")
      .select("id")
      .eq("date", date)
      .eq("period", period);
  
    if (attendanceError) {
      console.log(attendanceError);
      return;
    }
  
    if (attendanceData.length === 0) {
      console.log("Attendance record not found for the entered date and period.");
      return;
    }
  
    const attendanceId = attendanceData[0].id;
  
    const { data, error } = await supabase.from("Correction").insert([
      {
        name: "Aswin",
        date: date,
        Class_Id: 6,
        Std_Id: 13,
        Period: period,
        Status: false,
        Attendance: present,
        Attendance_Id: attendanceId, 
        user_id :'d3cd3887-fd01-4795-87f3-6008aaadf4c2',
      },
    ]);
  
    if (error) {
      console.log(error);
    } else {
      console.log("Your report has been placed successfully");
    }
  }
  
  const logOut = async () => {
    let { error } = await supabase.auth.signOut();
    $user = false;
    goto("/");
  };

  $: console.log($user);
  </script>
 
  <div class="attendance-container">
    <div class="attendance-card">
      <div class="table-container">
    
        <div class="header">
            <div class="container">
                <h4>Welcome {$user?.email ? $user.email : ""}!</h4>
                {#if $user.email}
                  <p on:click={logOut}>Logout</p>
                {/if}
              </div>
    <div class="h12">
            <h1>Attendance Table</h1>
            <div class="dropdown">
              <button class="report-btn">Report</button>
              <div class="dropdown-content">
                <div class="dropdown-option">
                  <label for="date-input">Date:</label>
                  <input type="date" id="date-input" />
                </div>
                <div class="dropdown-option">
                  <label for="period-input">Period:</label>
                  <input type="number" id="period-input" />
                </div>
  
                <div class="dropdown-option">
                  <label for="present-checkbox">Present</label>
                  <input type="checkbox" id="present-checkbox" />
                  <button class="send-button" on:click={sendData1}>Send</button>
                </div>
              </div>
            </div>
          </div>
        </div>
        
      </div>
  
      {#if attends.length > 0}
        <table class="attendance-table">
          <thead>
            <tr>
              <th> NO</th>
              <th>Date</th>
              <th>Period 1</th>
              <th>Period 2</th>
              <th>Period 3</th>
              <th>Period 4</th>
              <th>Period 5</th>
              <th>Period 6</th>
              <th>Period 7</th>
            </tr>
          </thead>
          <tbody>
            {#each attends as attend, i}
              <tr>
                <td>{i + 1}</td>
                <td>{attend.date}</td>
                {#each attend.periods as period}
                  <td style="color: {period.status ? 'green' : 'red'}"
                    >{period.status ? "Present" : "Absent"}</td
                  >
                {/each}
              </tr>
            {/each}
          </tbody>
        </table>
      {:else}
        <p>No attendance data found.</p>
      {/if}
    </div>
    
  </div>


  
  <style>
    .h12 {
      display: flex;
      justify-content: space-between;
    }
    .header {
      align-items: center;
      margin-bottom: 20px;
    }
  
    .send-button {
      background-color: #4caf50;
      color: white;
      padding: 8px 16px;
      border: none;
      border-radius: 4px;
      font-size: 16px;
      cursor: pointer;
    }
  
    .send-button:hover {
      background-color: #3e8e41;
    }
  
    /* .userh1 {
      font-family: Arial, sans-serif;
  
      letter-spacing: 2px;
      margin-top: 20px;
      font-size: 1.5em;
      font-weight: bold;
  
      margin-bottom: 0.5em;
    } */
  
    .table-container {
      position: relative;
    }
  
    button {
      position: absolute;
      top: 0;
      right: 0;
      background-color: red;
      color: white;
      padding: 10px 20px;
      border: none;
      border-radius: 4px;
      cursor: pointer;
      font-family: sans-serif;
      font-size: 16px;
      font-weight: bold;
    }
  
    .attendance-container {
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
      font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
    }
  
    .attendance-card {
      background-color: white;
      box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.2);
      border-radius: 10px;
      padding: 2rem;
    }
  
    .attendance-card h1 {
      font-size: 2rem;
  
      font-weight: bold;
    }
  
    .attendance-table {
      border-collapse: separate;
      border-spacing: 0;
      width: 100%;
      border: 2px solid #ddd;
      font-size: large;
      font-weight: bold;
    }
  
    .attendance-table th,
    .attendance-table td {
      border: 2px solid #ddd;
      padding: 0.5rem;
      text-align: center;
    }
  
    .attendance-table th {
      background-color: #f0f0f0;
      font-weight: bold;
    }
  
    .attendance-table tr:nth-child(even) {
      background-color: #f8f8f8;
    }
    .dropdown {
      position: relative;
      display: inline-block;
      margin-right: 20px;
    }
  
    .report-btn {
      background-color: red;
      color: white;
      font-size: 16px;
      border: none;
      padding: 10px;
      border-radius: 5px;
    }
  
    .dropdown-content {
      display: none;
      position: absolute;
      z-index: 1;
      top: 38px;
      right: 0;
      background-color: #f1f1f1;
      min-width: 160px;
      box-shadow: 0px 8px 16px 0px rgba(0, 0, 0, 0.2);
      border-radius: 5px;
    }
  
    .dropdown-option {
      padding: 10px 12px;
      font-size: 14px;
      display: flex;
      align-items: center;
    }
  
    .dropdown-option:last-child {
      border-bottom: none;
    }
  
    .dropdown-option input[type="number"],
    .dropdown-option input[type="date"] {
      border: none;
      background-color: #ffffff;
      padding: 6px 8px;
      color: #333;
      border-color: #000000;
      border-radius: 5px;
      margin-left: 10px;
    }
  
    .dropdown-option input[type="checkbox"] {
      margin-right: 10px;
    }
  
    .dropdown-option label {
      margin: 0;
      font-weight: bold;
    }
  
    .dropdown-content button.send-button {
      margin-top: 10px;
      padding: 8px;
      border: none;
      background-color: rgb(8, 206, 90);
      color: white;
      border-radius: 5px;
      cursor: pointer;
    }
  
    .dropdown:hover .dropdown-content {
      display: block;
    }
    .container {
    display: flex;
    justify-content: space-between;
    align-items: center;

    padding: 1rem;
   
  }

  h4 {
    font-size: 1.2rem;
    margin: 0;
    margin-right: 1rem;
  }

  p {
    font-size: 1rem;
    cursor: pointer;
    background-color: blue;
    color: white;
    padding: 0.5rem 1rem;
    border-radius: 4px;
  }

  p:hover {
    background-color: #0779c5;
  }
  </style> 
  