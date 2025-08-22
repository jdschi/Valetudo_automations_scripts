A few automations and script that have been useful with Valetudo in Home Assistant
 - `update_valetudo_vacuum_room_list.yaml`: Create a dropdown helper with the name `input_select.valetudo_room_list`. On creation it needs a single temporary entry.
   Edit this automation yaml for your robot (instead of `valetudo_p10purobot` in this yaml), and this automation will make the dropdown list for each room on your map with
   an attribute that is the segment number. Useful with the script.
