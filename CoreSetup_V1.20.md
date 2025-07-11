**CoreSetup Requirements V1.20**

Version: V1.20, 4/04/25  
Requirements Details  
Purpose: Define the setup behavior and UI elements for the CoreSetup
module, initializing the patient log and report framework using HTML,
CSS, and JavaScript for improved usability and visual feedback, fully
aligned with the debugged web implementation as of 4/04/25.  
Scope: Applies to the CoreSetup module for the web-based HealthSync
application. Covers initial page setup, log creation, default data
logic, UI enhancements, column structure initialization, multi-year log
management, log deletion, no-log handling, and row management for
spreadsheet-like functionality.

Requirements

Create New Log Button

- HTML: \<button id="create-log"\> on index.html and health-log.html,
  within .healthsync-button-container (index) or .log-controls
  (health-log).

- Label: "Create New Log".

- CSS: width: 110px; height: 26px; font-family: Arial; font-size: 9px;
  font-weight: bold; color: white; background-color: \#0066CC; border:
  1px solid \#0066CC; margin-left: 20px;

- JS Event: onclick opens a dialog box (\<div id="log-modal"
  class="modal"\>) in setup.js:  
  Dialog Box Details:

  - Information Requested:

    - \<input id="start-date" type="text" placeholder="Start Date
      (mm/dd/yyyy)"\>—requests log start date.

    - \<input id="end-date" type="text" placeholder="End Date
      (mm/dd/yyyy)"\>—requests log end date (optional).

    - \<input id="rows-per-day" type="text" placeholder="Rows per
      Day"\>—requests number of log rows per day (default: 5 or last
      used value).

    - \<input id="doctor-name" type="text" placeholder="Doctor's
      Name"\>—requests doctor’s name.

    - \<input id="patient-name" type="text" placeholder="Patient's
      Name"\>—requests patient’s name.

    - \<p\> note: "Enter date range for log entries (mm/dd/yyyy)."

  - Prompts:

    - Placeholders: "Start Date (mm/dd/yyyy)", "End Date (mm/dd/yyyy)",
      "Rows per Day", "Doctor's Name", "Patient's Name" guide input.

    - "Rows per Day" defaults to 5 on first use or recalls last used
      value from localStorage (healthsync.rowsPerDay).

  - Validation:

    - If Start Date, Doctor’s Name, or Patient’s Name is empty: Dialog
      box with “Please enter start date, doctor’s name, and patient’s
      name.” and "OK" button.

    - If Start Date or End Date format invalid: Dialog box with “Please
      enter the date in mm/dd/yyyy format (e.g., 01/01/2030).” and "OK"
      button.

    - If year exists: Dialog box with “Year YYYY already exists. Click
      OK to overwrite, or Cancel to keep the existing log.” and
      "OK"/"Cancel" buttons.

    - If 5-year limit reached: See "5-Year Limit Dialog Box" below.

    - If "Rows per Day" is not a positive integer: Dialog box with “Rows
      per Day must be a positive number.” and "OK" button.

    - If total rows exceed 500 (days between Start and End × Rows per
      Day): Dialog box with “Max 500 rows—adjust dates or rows per day.”
      and "OK" button.

  - Look and Feel:

    - Dialog box: position: fixed; top: 50%; left: 50%; transform:
      translate(-50%, -50%); background-color: white; border: 1px solid
      \#ccc; padding: 20px; z-index: 1000; width: 200px; height: 230px;

    - Inputs: width: 80px; height: 20px; margin: 10px 0; in a centered
      flex column (.modal-content).

    - Note: font-size: 12px; color: \#333; margin: 10px 0;

    - Buttons: \<button id="modal-ok"\>OK\</button\>, \<button
      id="modal-cancel"\>Cancel\</button\>—width: 100px; height: 20px;
      margin: 5px 10px; cursor: pointer;

  - Post-Press Behavior:

    - Button remains 110px x 26px, \#0066CC, unchanged post-click.

    - Dialog box appears above table, blocking interaction until closed.

    - On "OK": Validates inputs, calculates days between Start and End
      (if End is empty, use Start for one day), multiplies by Rows per
      Day, pre-fills Column A (Date) with dates, leaves Column D (Time)
      blank, stores in healthsync.logs, sets Year field in
      .log-controls, saves Rows per Day to localStorage, redirects to
      health-log.html (no alert).

    - On "Cancel": Dialog box closes, button unchanged.

  - JS: Sets Column A rows to date range (mm/dd format); Enter key
    triggers OK. Requires local server (Debug Session 10, 3/30/25).

  - Note: Initially positioned with position: absolute (e.g., left:
    250px; top: 55px), adjusted to .log-controls after multiple
    iterations (Session 7) to align with log book layout.

- Initial Table Setup: On "OK", initializes the table with rows based on
  date range and Rows per Day, Column A pre-filled with dates (mm/dd),
  Column D (Time) blank, followed by one row with mm/dd placeholder.

5-Year Limit Dialog Box

- Trigger: Attempting to create a 6th year log via "Create New Log".

- Content:

  - Text: "You have reached the 5-year limit. Would you like to save the
    oldest log (Year YYYY) as a CSV file before removing it?"

  - Buttons: "Save", "Do not save", "Cancel".

- Look and Feel: White background, centered, 400x100px, black text,
  buttons 80x30px.

- Position: Centered on screen, blocks interaction until closed.

- Behavior:

  - "Save": Exports oldest log to CSV, opens "Save Confirmation Dialog
    Box", removes oldest log, adds new log, reloads page.

  - "Do not save": Removes oldest log, adds new log, reloads page.

  - "Cancel": Closes dialog box, no action taken.

Save Confirmation Dialog Box (5-Year Limit)

- Trigger: Clicking "Save" in the 5-year limit dialog box.

- Content:

  - Text: "Health Log YYYY was saved in the Download directory."

  - Button: "OK".

- Look and Feel: White background, centered, 300x100px, black text, blue
  "OK" button (80x30px).

- Position: Centered on screen, blocks interaction until closed.

- Behavior: On "OK", closes dialog box, proceeds with log creation.

Edit Log Button

- HTML: \<button id="edit-log-info"\>Edit\</button\> on health-log.html,
  within .log-controls.

- CSS: width: 110px; height: 26px; font-family: Arial; font-size: 9px;
  font-weight: bold; color: white; background-color: \#0066CC; border:
  1px solid \#0066CC;

- JS Event: onclick opens a dialog box (\<div id="edit-log-form"\>) in
  setup.js:  
  Dialog Box Details:

  - Content:

    - Text: "To change the start date, please change the date under the
      1st row of the Date column."

    - Inputs: \<input id="edit-year"\>, \<input id="edit-doctor"\>,
      \<input id="edit-patient"\> (pre-filled with current values).

    - Buttons: \<button id="save-log-info"\>Save\</button\>, \<button
      id="cancel-edit"\>Cancel\</button\>.

  - Look and Feel: White background, 300x150px, black text, inputs
    200x30px, buttons 80x30px (side by side).

  - Position: Centered between "Doctor" and "Patient" labels in
    .log-controls, blocks interaction until closed.

  - Behavior:

    - "Save": Updates year, doctorName, and patientName in
      healthsync.logs, reloads page. Validates year as a 4-digit number
      and checks for year collisions with overwrite confirmation.

    - "Cancel": Closes dialog box, no changes.

Actions Dropdown

- HTML: \<select id="log-actions"\> on health-log.html, within
  .log-controls.

- CSS: Inherits default \<select\> styling (approx. width: 150px;
  height: 20px; per browser defaults).

- JS: Options in setup.js:

  - Default: \<option value=""\>Actions\</option\> (placeholder, visible
    in dropdown).

  - "Save to CSV": \<option value="save"\>Save to CSV\</option\>—exports
    the currently selected log year (from \#year-select) to
    HealthSync_Log_YYYY.csv using log.tableData:

    - Metadata: Year,YYYY, Doctor,doctorName, Patient,patientName.

    - Empty row.

    - Group headers: "Accumulated" at J (col 9), P (col 15).

    - Headers from COLUMN_CONFIG.

    - Data: All rows with valid dates in Column A (non-empty, not
      mm/dd), including Column A (Date) and Column D (Time) data when
      present.

    - Opens "Save to CSV Confirmation Dialog Box".

  - "Delete": \<option value="delete"\>Delete Log\</option\>—opens
    "Delete Log Confirmation Dialog Box".

- JS Event: onchange triggers action, resets to "" post-action.

- Look and Feel: Dropdown expands downward when clicked, showing
  options.

- Note: Resolved issue where CSV export initially failed with Date
  column data by using log.tableData (Debug Session 11). Fixed issue
  where Row 1 date was not included in the 2030 log by ensuring
  saveTableDataToLog captures the latest table data before exporting.

Save to CSV Confirmation Dialog Box

- Trigger: Selecting "Save to CSV" from \#log-actions.

- Content:

  - Text: "Health Log YYYY was saved in the Download directory."

  - Button: "OK".

- Look and Feel: White background, centered, 300x100px, black text, blue
  "OK" button (80x30px).

- Position: Centered on screen, blocks interaction until closed.

- Behavior: On "OK", closes dialog box.

Delete Log Confirmation Dialog Box

- Trigger: Selecting "Delete Log" from \#log-actions.

- Content:

  - Text: "Are you sure you want to delete the log for YYYY?"

  - Buttons: "OK", "Cancel".

- Look and Feel: White background, centered, 300x100px, black text, blue
  "OK" button (80x30px).

- Position: Centered on screen, blocks interaction until closed.

- Behavior:

  - "OK": Deletes log, updates healthsync.logs, reloads page. If no logs
    remain, resets to initial state (welcome message, one row with
    mm/dd). If logs remain, selects the latest year.

  - "Cancel": Closes dialog box, no action.

- Note: Deletion logic re-evaluated if year modified in Column A Row 1,
  triggering sync with \#year-select.

Multi-Year Log Management

- JS: Stores up to 5 years in localStorage (healthsync.logs) as {year,
  startDate, doctorName, patientName, tableData}.

- JS: 6th year triggers "5-Year Limit Dialog Box".

- JS: Year collision triggers a dialog box with overwrite confirmation.

- HTML/JS: \<select id="year-select"\> in .log-controls:

  - Lists years, updates table/log info on change, saves current
    tableData via saveTableDataToLog().

  - Syncs with Column A Row 1: Editing date updates startDate,
    re-populates dropdown; changing dropdown updates table/date.

- Note: Initial sync issues resolved by re-populating dropdown on Row 1
  edits (Session 7).

- CSS: .log-controls { display: flex; align-items: center; gap: 20px;
  margin: 20px; }

Navigation Bar

- HTML: \<nav\> on health-log.html: Links "Log", "Personal Info",
  "Doctor Report", "Calc Reference" (no "Home").

- CSS: background-color: \#0066CC; color: white; padding: 10px;
  position: sticky; top: 0;

- Responsive: @media (max-width: 600px) toggles via nav.js.

Log Structure and Initialization

- HTML: \<table id="health-log"\> on health-log.html.

- JS: Initializes 26 columns (A-Z) via COLUMN_CONFIG (setup.js):

  - A: Date (I)

  - B: Day of Wk (O)

  - C: Day of Diet (O)

  - D: Time (I)

  - E: Event (L, empty \<select\>)

  - F: Event Description (I)

  - G: Event Value (I)

  - H: Event Units (O)

  - I: Sat Fat (g) (I)

  - J: Carbs/Day (g) (O)

  - K: Walked off Carbs (g) (O)

  - L: Exercise Carbs (g) (O)

  - M: Total Carbs = Exercise + Diet (g) (O)

  - N: Miles to Walk to burn Carbs (O)

  - O: (Grams of carbs burned)/(mile walked) (O)

  - P: Accum Sat Fat/Day (g) (O)

  - Q: Walked Sat Fat (miles) (O)

  - R: Ex Sat Fat (g) (O)

  - S: Total Sat Fat = Exercise + Diet (g) (O)

  - T: Miles to Walk off Sat Fat (O)

  - U: (Grams of Sat Fat burned)/(mile of exercise) (O)

  - V: Weight (lbs) (I)

  - W: Notes (I)

  - X: A1C (%) (O)

  - Y: LDL (mg/dL) (O)

  - Z: HDL (mg/dL) (O)

- JS: Initial state (no logs): One row with Column A showing mm/dd
  placeholder, Column D (Time) blank. After log creation: Rows based on
  Start/End Dates and Rows per Day, Column A with dates, Column D blank,
  followed by one row with mm/dd placeholder.

- JS: Dynamic row addition: On Enter in the last row’s Date field, adds
  a new row with mm/dd placeholder (only if a log exists).

- JS: Input handling in initial state: Any input in columns A, D, E, F,
  G, I, V, or W triggers a modal with "Please press Create New Log
  button to start the Log." and clears the input.

- JS: tableData stored via saveTableDataToLog().

- JS: Row management via right-click context menu:

  - Trigger: Right-click on a table row (\<tr\>).

  - Content:

    - Options: "Delete Row", "Add Above", "Add Below".

    - Multi-row selection: Ctrl+click to select multiple rows;
      right-click applies to all selected.

  - Behavior:

    - "Delete Row": Removes the selected row(s). If all rows are
      deleted, leaves one row with mm/dd placeholder in Column A, Column
      D blank.

    - "Add Above": Inserts copies of the selected row(s) above the
      topmost selected row. Copies only Input/List columns (A: Date, D:
      Time, E: Event, F: Event Description, G: Event Value, I: Sat Fat
      (g), V: Weight (lbs), W: Notes); Output columns (B, C, H, J-Z) are
      blank.

    - "Add Below": Inserts copies of the selected row(s) below the
      bottommost selected row. Copies only Input/List columns; Output
      columns are blank.

    - Total rows capped at 500; if exceeded, shows dialog box: "Max 500
      rows—adjust rows." with "OK" button.

  - Look and Feel:

    - Context menu: position: absolute; at click coordinates, white
      background, border: 1px solid \#ccc, options as a vertical list,
      each 100x20px.

    - Selected rows: Highlighted with background-color: lightblue; via
      .selected class.

  - Note: Output columns to be recalculated by the Calculations module
    in future versions.

- JS: No logs: \<div id="welcome-message"\> shows "Welcome to
  HealthSync! No logs found. Please click the 'Create New Log' button to
  get started." above table; Column A Row 1 shows mm/dd placeholder,
  Column D blank.

- CSS:

  - \<thead\>: \#00FFFF, black, font: bold 8px Arial.

  - Rows: \#FFFFFF (day 1), \#F0F0F0 (day 2), repeating.

  - Widths: A: 80px, B: 60px, C: 60px, D: 80px, E: 100px, F: 150px, G:
    60px, H: 60px, I: 60px, J: 48px, K: 45px, L: 55px, M: 55px, N: 60px,
    O: 48px, P: 60px, Q: 45px, R: 60px, S: 63px, T: 60px, U: 48px, V:
    60px, W: 150px, X: 60px, Y: 60px, Z: 60px.

  - \#welcome-message: \#e6f3ff, border: 1px solid \#4a90e2, font: bold
    18px Arial.

  - Font Size: All table inputs and td elements set to font-size: 10px
    for consistency.

  - Column D (Time): \<input\> with placeholder: "hh:mm", tooltip:
    "24-hour or AM/PM".

Log Formatting

- CSS: Output \<td\> class output; Input \<td\>: font-weight: bold;
  border: 1px solid;

- HTML: Log info (Year, Doctor, Patient) in .log-controls, editable via
  Edit button.

- HTML: Table header note: "Right-click rows to add/delete."

Dependencies

- HTML5, CSS3, ES6 JS; styles.css, nav.js.

Verification Steps

- Confirm "Create New Log" (20px left in flex):

  - Opens dialog box (200x230px, 80px inputs, 100px buttons), requests
    Start Date/End Date/Rows per Day/Doctor/Patient, shows placeholders
    and note.

  - "Rows per Day" defaults to 5 or last used value (check
    localStorage).

  - Prompts on invalid input (300x100px dialog boxes for empty fields,
    bad date format, invalid Rows per Day, year collision, 5-year limit,
    row limit).

  - Post-click: Button unchanged (110px x 26px, \#0066CC), dialog box
    blocks interaction, redirects on OK, sets Year field.

  - Initializes table with rows based on date range and Rows per Day:
    Column A with dates (mm/dd), Column D (Time) blank, followed by
    mm/dd placeholder row.

- Verify dynamic row addition: Pressing Enter in the last row’s Date
  field adds a new row with mm/dd placeholder (only if a log exists).

- Check "Edit" button (in .log-controls): Opens dialog box (300x150px)
  between Doctor and Patient labels, updates year/doctor/patient; Column
  A Row 1 syncs with \#year-select.

- Verify \#log-actions (in .log-controls, ~150px): Saves any selected
  year to CSV (300x100px confirmation dialog box) with Column A and D
  data, deletes any selected year (300x100px confirmation dialog box),
  expands downward.

- Confirm no logs: Welcome message appears, disappears on log creation,
  Column A Row 1 shows mm/dd, Column D blank. Any input in A, D, E, F,
  G, I, V, or W triggers "Please press Create New Log button to start
  the Log." modal, clears input, and prevents row addition.

- Check \<nav\> (no "Home"), table (one row initially, multiple after
  log creation), widths as coded, font size 10px across all columns.

- Verify CSV export includes all rows with valid dates for both "Save to
  CSV" and 5-year limit save, including Column D (Time) data.

- Confirm Time column (D): 80px width, \<input\> with
  placeholder="hh:mm", tooltip "24-hour or AM/PM", blank by default.

- Verify row management:

  - Right-click on row opens context menu with "Delete Row", "Add
    Above", "Add Below".

  - Ctrl+click selects multiple rows, highlighted with background-color:
    lightblue;.

  - "Delete Row": Removes selected row(s), leaves one mm/dd row if all
    deleted.

  - "Add Above/Below": Copies Input/List columns (A, D, E, F, G, I,
    V, W) above topmost/below bottommost, leaves Output columns (B, C,
    H, J-Z) blank.

  - Row cap at 500, with 300x100px dialog box on excess.

  - Table header note: "Right-click rows to add/delete" visible.

Status

- Complete: Debugged by 4/04/25, updated to match planned
  implementation.

Changes from V1.19

- Added Time column (D) between Day of Diet (C) and Event (E), shifting
  subsequent columns (E-Z). Type: Input, blank by default, accepts
  24-hour (hh:mm) or 12-hour (hh:mm AM/PM), stored as 24-hour. Width:
  80px, placeholder: "hh:mm", tooltip: "24-hour or AM/PM". Sorting
  deferred to DataEntry module (Session 13).

- Updated "Create New Log" dialog: Replaced Start Date with Start Date
  and End Date (optional), added Rows per Day (default 5 or last used,
  stored in localStorage), added note "Enter date range for log entries
  (mm/dd/yyyy)". Updated size to 200x230px. Added validations: Rows per
  Day as positive integer, 500-row cap. Pre-fills Column A with dates,
  Column D blank (Session 13).

- Added row management via right-click context menu: "Delete Row", "Add
  Above", "Add Below". Supports multi-row selection via Ctrl+click,
  highlighted with .selected class. Add copies Input/List columns (A, D,
  E, F, G, I, V, W), leaves Output columns (B, C, H, J-Z) blank for
  future recalculation. Row cap at 500. Added table header note:
  "Right-click rows to add/delete" (Session 13).

- Updated column count to 26 (A-Z), adjusted COLUMN_CONFIG and widths
  accordingly.

- Enhanced CSV export to include Column D (Time) data.

- Added verification steps for Time column, updated "Create New Log"
  behavior, and row management functionality.
