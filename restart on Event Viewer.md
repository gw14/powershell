The Event Viewer in Windows is the primary tool for reviewing detailed logs of system activities, including shutdowns and restarts. Here's how you can check these events:

-----

## Access Event Viewer

  * **Using Run dialog:** Press `Windows Key + R`, type `eventvwr.msc`, and press Enter.
  * **Through Start Menu:** Right-click on the Start button, choose "**Windows PowerShell (Admin)**" or "**Windows Terminal (Admin)**", then type `eventvwr` and press Enter. You can also search for "**Event Viewer**" in the Start menu.

-----

## Navigate to System Logs

In the Event Viewer window, in the left pane, expand "**Windows Logs**" and then click on "**System**". This log contains events related to system activities, including shutdowns and startups.

-----

## Filter for Shutdown/Restart Events

The System log contains a vast number of events, so it's best to filter it to find the specific restart/shutdown events you're looking for.

  * In the right pane of the Event Viewer, click on "**Filter Current Log...**".

  * In the "Filter Current Log" dialog box, in the **\<All Event IDs\>** field, enter the relevant Event IDs separated by commas. Here are the most common Event IDs for shutdown and restart events:

      * **Event ID 1074:** This event indicates a planned shutdown or restart initiated by a user or an application (like Windows Update). It often provides details about the user account, the process, and the reason for the shutdown.
      * **Event ID 6006:** This event indicates a clean or proper shutdown of the system. The message is typically "The Event log service was stopped."
      * **Event ID 6005:** This event indicates a system startup. The message is typically "The Event log service was started."
      * **Event ID 6008:** This event signifies an unexpected shutdown (also known as a "dirty shutdown"). This can happen due to a power loss, system crash, or abrupt shutdown.
      * **Event ID 41:** This event is logged when the system reboots without cleanly shutting down first. It often indicates a system crash, power failure, or a hard reset (like holding the power button). It might also include bug check data if a Stop error occurred.

    For example, to see all common shutdown and restart events, you would enter: `41, 1074, 6005, 6006, 6008`

  * Click "**OK**" to apply the filter.

-----

## Review the Logs

Once the filter is applied, you will see a list of shutdown and restart events. You can click on each event to view its details in the "**General**" and "**Details**" tabs at the bottom.

**Key Information to Look For in Event Details:**

  * **Date and Time:** When the event occurred.
  * **Source:** The component that generated the event (e.g., User32, EventLog, Kernel-Power).
  * **Event ID:** The specific identifier for the event.
  * **User:** For Event ID 1074, it will often show the user who initiated the shutdown/restart.
  * **Description/Message:** Provides details about why the event occurred. For Event ID 1074, it might specify the reason code or the application that triggered the action.

By examining these events, you can create a timeline of your system's shutdown and restart history and potentially identify the cause of any unexpected reboots.
