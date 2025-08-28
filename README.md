# A script and a few automations for Valetudo

I find it useful to have a script for utilizing automation with Valetudo. The script uses fields, so that you get something that looks like this when you write an automation, and makes sure
that any pecularities to a given room can be maintained (lights, blinds, water flow, etc.). It can be called several times and the robot will then clean each room in the order that it is called.

![Screenshot](https://github.com/jdschi/Valetudo_automations_scripts/blob/main/Screenshot_20250825_075917.png?raw=true)

There is a simpler implementation involving two blueprints.

To implement the blueprints:
 1. Open Valetudo and open the hamburger at top left  Maps -> Segment Management. Insure that your segments are descriptively named, like "Kitchen".
 2. Import the blueprints to your `config/blueprints/automation` and `config/blueprints/script` folders. Go to Developer Tools and reload Automations and then Scripts.
 3. Open the "Update Valetudo Room List" blueprint, and find the robot "_map_segments" sensor in the first box. In the second box scroll to the bottom of the menu and
    create a helper called 'input_select.valetudo_room_list` or similar. It needs a single entry on creation---use "Rage Against the Machine".  Remember the name of
    the dropdown you just created for the next step. Click on the three dots on the upper right and Run the script. You can check your new "input_select" entity to
    make sure that it is populated with the room names from Step 1.
 4. Open the script blueprint and fill it out with your robot information, and add your new dropdown "input_select" at the bottom. When you are finished, save it and
    run it. You should see a menu. Pick a room and click run. Your robot should head towards that room. If you want, choose a different room, and click run again. The
    robot should wait until the first job is finished. 


These are optimized for [Valetudo](https://valetudo.cloud) in [Home Assistant](https://www.home-assistant.io/). 

I guess that it grew so big from years of tweaking. I enjoy anyone in the family being able to say a simple sentence to my $13 Atom Echo like _OK Nabu, mop the downstairs kitchen_ and it 
turns off the radio, turns on the lights, opens the curtains and sends the robot to mop up. When it is done, it returns the lights, curtains and radio back to the prior state,
and the robot goes home to clean and dry the mop pads. All **local**. I even get a message with the map showing the route taken by the robot. I am sure that someone else could code it 
better, and it would be nice if it could be turned into something like a blueprint or integration.


 - `update_valetudo_vacuum_downstairs_room_list.yaml`: First create a dropdown helper with the name `input_select.valetudo_downstairs_room_list`. On creation it needs a single temporary entry.
   Edit this automation yaml for your robot (instead of `valetudo_p10purobot`), and this automation will make the dropdown list for each room name on your map with
   an attribute that is the segment number, and keep it up to date. Necessary for the script. 
   
 - `clean_route_rest_command.yaml`: example of adding a rest command to Home Assistant for the robot quirks. Comments explain how to obtain details from the Swagger UI. It is used in the script, but
   is not really essential.

 - `clean_selected_downstairs_room_with_robot.yaml`: Script to clean any room on your Valetudo map within Home Assistant. Makes it easier to add multiple ways to clean a room (_e.g._, from
   zigbee remote, timer, absence of occupants, etc). 

   It allows quirks for each room, like turning on lights while cleaning and then resetting them to prior state. This helps obstacle avoidance. Similar idea with music, blinds, etc.
   It requires the automation `update_valetudo_vacuum_downstairs_room_list.yaml`, and provisionally `clean_route_rest_command.yaml`. The latter could easily be removed from the script if undesired.
   Or other quirks could be added.  The options list includes only a few rooms and lights as examples.

 - The script also takes advantage of this useful [docker container](https://github.com/erkexzcx/valetudopng) which allows the telegram message to have a map image. Remove or disable
   this notification if you like, or are not using that container.

 - **Light-weight voice assistant.** If you have [Ollama](https://ollama.com/) running at home, and the [Ollama integration in Home Assistant](https://www.home-assistant.io/integrations/ollama/), there
   is a very low-resource way to use voice assistant. Set up a new agent in the Ollama integration using Llama 3.2:3b. Leave the boxes unchecked to control HA, so there are no entities exposed. It
   should not think before it responds, and keep the maximum memory messages to 0. All of these settings should make it pretty fast on a reasonable CPU. The zero memory seems to be essential to
   keep the LLM from second guessing itself on successive calls.  I named the agent "list select Llama3.2". Give it the prompt
   
   > You will be given a list, where the members are separated by commas. You should find the member of the list that is most similar to the given object.
   > Your response should be exactly the member only with no other text or brackets. it should be exactly what is in the list. Do not give an explanation. Be sure that your answer is inside  quotation marks.

   Then the automation `voice_activated_cleaning.yaml` should work for calling the robot using the above script.
   You might want to find sentences that seem more natural to you, but now the name you use for the word is
   extremely flexible. I have tried things that are pretty far away from "Master Bedroom",  "Hallway", or "Living room" (_e.g._, "big bedroom", "corridor", "parlor") and it knows what I want.
   There are failures, like it doesn't seem to know one sleeps in bedroom.

I like the idea of using smaller LLMs (SLMs?) to do simpler tasks in HA. It seems to me that much of the use in real time is like this: just pick the best member of a list. A python script could probably
do this well enough. I am sure that a smaller LLM could do this job even more efficiently than Llama 3.2 does, but I could not get others to return only what I needed. 
(I tried smoLLM, smoLLM2, Qwen, tinyLLM, deepseek-r1:1.5b.) This automation actually uses 3 small
LLMs: one for STT, one for TTS and then my simple list select.

A true LLM would probably be more useful to look at your patterns of behaviour, suggest automations and then write them. In other words, tasks not done in real time.
