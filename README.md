A few automations and a script that have been useful with Valetudo in Home Assistant

 - `update_valetudo_vacuum_room_list.yaml`: First create a dropdown helper with the name `input_select.valetudo_room_list`. On creation it needs a single temporary entry.
   Edit this automation yaml for your robot (instead of `valetudo_p10purobot`), and this automation will make the dropdown list for each room on your map with
   an attribute that is the segment number, and keep it up to date. Necessary for the script.
   
 - `clean_route_rest_command.yaml`: example of adding a rest command to Home Assistant for the robot quirks. Comments explain how to obtain details from the Swagger UI.

 - `clean_selected_room_with_robot.yaml`: Script to clean any room on your Valetudo map within Home Assistant. Makes it easier to add multiple ways to clean a room (e.g., from
   zigbee remote, timer, absence of occupants, etc. It is in queue mode, so you can call it multiple times, and it will then successively clean each room called.

   It allows quirks for each room, like turning on lights while cleaning and then resetting them to prior state. This helps obstacle avoidance. Similar idea with music, blinds, etc.
   It requires `update_valetudo_vacuum_room_list.yaml`, and provisionally `clean_route_rest_command.yaml`. The latter could easily be removed from the script if undesired.
   Or other quirks could be added.  The options list includes only a few rooms and lights as examples.
