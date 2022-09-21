<!-- ==================== Intro Section ==================== --> 			

While using natural language processing within a chatbot allows for more natural interaction with an end user, it can sometimes be difficult to fully replicate the ability of a live agent to both visualize the real-world object being discussed as well as respond to the user's reactions during the discussion. We investigate how integrating various Oracle Cloud Infrastructure (OCI) AI services can help drive the nature of the conversation. Experiment with enhancing Oracle Digital Assistant so the chatbot can recognize physical objects being discussed and react to user sentiment in conversation._

## Before you Begin
In this tutorial, you're going to build out a partially built “Pizza skill, to bring in an “order pizza” process as well as introducing the use of the OCI AI services to expand on the native Natureal Language Processing (NLP) capabilities of the Oracle Digital Assistant. functionality that will flow to an existing skill that already has a completed cancel order flow.

### Background
Oracle Digital Assistant is an environment for building _digital assistants_, which are user interfaces driven by artificial intelligence (AI) that help users accomplish a variety of tasks in natural language conversations. Digital assistants consist of one or more _skills_, which are individual chatbots that are focused on specific types of tasks.

In this lab, you will create a skill that can be used for interactions with a pizzeria, including ordering pizzas and canceling orders. As part of this process, you will:
 - Define intents, utterances, entities.
 - Design a conversation flow.
 - Validate, debug and test your skill.

### What Do You Need?
 - Access to the OCI Console  - As used to provision the instance of the Oracle Digital Assistant.

**Note:** If you are using an Oracle hosted ODA instance for this tutorial, the required OCI service permissions as described in section 1 below have been already defined and you could skip that section.  If you are using your own OCI environment (such as a Trial instance) please perform the steps in section 1 prior to building out your Digital Assistant.</

 - The starter skill, **AI_Service_Starter_Skill.zip**, which is included with this tutorial. Download this file to your local system.
 
 - Access to Oracle Digital Assistant Version 22.06 or higher
		







<!-- ==================== Step 1 Begin ==================== --> 

## The AI-Service-Starter-Skill (Pizza) Skill

<!-- 
====================================================================
= REVIEW THE STARTER SKILL                                         =
====================================================================
-->
### Review the Starter Skill

<p>If you have not already done so import the AI-Service-Starter-Skill.zip and open the skill by clicking on the tile. Then take a look at the following artifacts.

1. <img src="./images/left_nav_intents.png" width="48px"> -- **Intents**: Take a look at the pizza.reg.orderPizza, pizza.reg.cancelPizza, pizza.ans.calories, and ans.proc.vegetarianPizza intents. Note the conversation names for the pizza.ans.calories, and ans.proc.vegetarianPizza intents. In this tutorial, you'll either create flows for these intents, or reference them when you branch the dialog flow.
	
2. <img src="./images/left_nav_entities.png" width="48px"> -- **Entities**: You'll declare variables for two of the composite bag entities: cbe.pizza, which supports the pizza menu, and cbe.confirmation, which is used to branch the flow. The resolution logic for cbePizza is executed through an entity event handler.	
	
3. <img src="./images/left_nav_resourceBundles.png" width="48px"> -- **Resource Bundles**: Following best practices, the prompts in this tutorial are executed through resource bundles, not through text strings. You'll reference the following resource bundles in your flow:
 | Property | Value |
 | ----------- | ----------------- |
 | answerIntentIntroMessage1 | Hello! You were asking about |
 | answerIntentIntroMessage2 | Here's your answer: |
 | cbe.confirmation.message | Your order is on the way. |
 | cbe.confirmation.prompt1 | You ordered a {0} {1} pizza. Is that right? |
 | pizza.messages.orderPizzaStart | Hey, there! Let's get this order started! |
 | systemFlowName_ans.about.calories | the calorie content of our pizzas |
 | systemFlowName_ans.proc.vegetarianPizza | vegetarian pizza |
 
 4. <img src="./images/left_nav_flows.png" width="48px"> -- **Reference flows**: This skill includes a set of reference flows to help you out if you run into problems. These flows are meant only as guides, not as functioning flows. Because they are not mapped to any events or reference any variables, the Visual Flow Designer notes these flows as having errors. You can ignore them.

	

## Extend the Starter Skill
<!-- 
====================================================================
= CREATE SKILL LEVEL VARIABLES                                     =
====================================================================
-->
### Create Skill-Level Variables
You're going to start off by declaring a variable for the cbe.pizza entity.  This variable will set the values for the pizza size and type. Because both the parent flow and the child flows need these values, you must create a skill-level variable so that all flows can access the values.


1. Click **Flows** in the left navbar.
2. Click **Main Flow**.
3. Click **Skill Variables.**.
4. Click **Add Variable** (located under **Custom Variables**).

| Property | Value |
| ----------- | ----------------- |
| Name | pizza |
| Description | Resolves the pizza menu. |
| Variable Type | Select **Entity** as the variable type, then select **cbe.pizza** as the entity name |


5. Click **[Apply]**


<!-- 
====================================================================
= BUILD THE ANSWER FLOW                                            =
====================================================================
-->
### Build the Answer Intent Flow
In this step, you're going to create an answer intent that handles user questions about calories and vegetarian options. In Visual Flow Designer, you don't need to create generic answer intent flow like this because the Main Flow presents answer intents automatically. But through the simple answer intent flow that you'll create in this step, you learn about creating and customizing a flow for a standard event, the group of Main Flow-level events for handling standard use cases like unresolved intent, answer intent, and dialog error.


1. Click **Add Flow**

2. In the Create Flow dialog filling the properties as indicated tin the following table and click **[Create]**

| Property | Value |
| ----------- | ----------------- |
| Name | pizza.ans.genericHandler |
| Description | Generic answer intent flow.  |
 

3. In the dialog flow editor, hover over the Start node, click the menu  , then click **Add start state**.

4. Select **User messaging**, then **Display Multimedia Messages**.

5. Choose **Display Intent answer** and fill in the dialog with the following, then click **[Insert]**

| Property | Value |
| ----------- | ----------------- |
| Name | Accept the default name (displayIntentAnswer). |
| Description | General answer |



When you're done, the flow should include the **displayIntentAnswer**displayIntentAnswer state:


6. Select the **displayIntentAnswer** state in the flow to open its property inspector.

7. Click **Component** in the property inspector. Then click **Edit Response Items**.

8.Take a look at the Apache FreeMarker expression in the text property that generates a default message based on the answer intent:

`
"${skill.system.event.value.intent.answer}"	
`

9. The default expression returns only the answer. To create a friendlier message that includes a greeting, and the conversation name, update the text property with the following expression. Then click **[Apply]**.

`"${rb('answerIntentIntroMessage1')} ${rb('systemFlowName_'+skill.system.event.value.intent.intentName)}. ${rb('answerIntentIntroMessage2')} ${skill.system.event.value.intent.answer}"`

This expression references the following resource bundles to enable the skill to output "Hello! You were asking about _{conversation name}_. Here is your answer: _{answer text}_.</
 - answerIntentIntroMessage1 -- Hello! You were asking about
 - systemFlowName_ -- Resource bundle for conversation name strings.
 - answerIntentIntroMessage2 -- Here's your answer:



<!-- 
====================================================================
= MAP ANSWER INTENT TO AN EVENT                                    =
====================================================================
-->
### Map The Answer Intent To An Event
You've completed the answer intent flow, but it can't yet display any answers. To trigger this flow when users ask about calories or vegetarian options, you need to return to the Main Flow to map it to an event that's dedicated to handling answer intents.

1. Click **Main Flow**Main Flow

2. If it's not already open, click **Events**.

3. Click <img src="./images/add_action_icon.png"> next to Built-In Events.

4. Select **Answer Intent** from the Unhandled Event Type list.

5. Select **pizza.ans.genericHandler** as the mapped flow. Then click Create.


<!-- 
====================================================================
= TEST THE ANSWER INTENT                                           =
====================================================================
-->
### Test The Answer Intent
Now we'll use the Skill Tester to make sure that the intent is resolved correctly and also see how the flow works.

1. Before you can chat with the skill, be sure that it's been trained with Trainer Tm:

 - Click **Train**


   <img src="./images/Train.png" width="500px">

 - Choose **Trainer Tm**, then click **[Submit]**	


2. Click **Preview** to open the Skill Tester.


<img src="./images/Preview.png" width="500px">


3. Enter _How many calories?_

4. Click **Reset**, then enter _Can I get a vegetarian pizza?_

5. For both of these responses, take a look at the traversal from the Main Flow to the **displayIntentAnswer** state of the **pizza.ans.genericHandler** flow.

6. When you're done, click Reset and then close the Skill Tester.




<!-- 
====================================================================
= CREATE THE ORDER PIZZA FLOW                                           =
====================================================================
-->
## Create The Order Pizza Flow
In this step, you're going to create the skill's primary flow.

1. Click **[+ Add Flow]** button to add a new intent-driven flow to the skill using the following properties.	

        | Property | Value |
        | ----------- | ----------------- |
        | Name | intent.reg.order |
        | Description | Order pizza |
        | Description | pizza.reg.orderPizza |
 
- Check **“open created flow afterwards”** and click **[Create]**


<!-- 
====================================================================
= CREATE THE ORDER PIZZA FLOW                                      =
====================================================================
-->
### Create The Flow Level Variable
<p>
In this step, you're going to declare a variable for the **cbe.confirmation** entity, whose YES | NO options support the branching action that you'll add to the flow later on. Because the branching action is unique to this flow, the value set for this variable needs to be confined to this flow only. Not only is its usefulness limited to the flow, but its lifespan is also. Unlike the skill-scoped pizza variable you created earlier, you're going to create this flow-scoped variable within the **intent.reg.order** flow, not within the main flow.	

1. Click on the **intent.reg.order** flow in the list the **Configuration** Tab

2. Click **Add Variable** and complete the dialog with the following values, then click **[Apply]**

        | Property | Value |
        | ----------- | ----------------- |
        | Name | confirmation |
        | Description | Flow branching variable |
        | Variable Type | Entity |
        | Variable Name | cbe.confirmation |


<!-- 
====================================================================
= BUILD OUT THE ORDER PIZZA FLOW                                   =
====================================================================
-->

### Build Out The Order Pizza Flow
For this short flow, you'll create a state for the skill-scoped composite bag pizza entity along with two other states that output text messages for greeting the user and confirming the order.

1. Click **Flow**.

2. Hover over the menu in the Start node, then click **Add start state**.  Select a **Send Message** component.

3. Set the properties for the component and click **[Insert]** to update the flow.

        | Property | Value |
        | ----------- | ----------------- |
        | Name | startPizzaOrder |
        | Description | Greeting Message |
	
4. Click the **startPizzaOrder** state to open its property inspector. Click **Component** if the Component page is not already open.

5. Instead of entering a text string for the greeting message, reference a pre-defined resource bundle:

`${rb('pizza.messages.orderPizzaStart')}`

6. Hover over the Start node, click the menu <img src=./images/menu_icon.png">, then click **Add state**.

7. Choose **Resolve Composite Bag**, and fill in the properties as:

        | Property | Value |
        | ----------- | ----------------- |
        | Name | resolvePizza |
        | Description | Pizza Menu |

Note the next transition line that's inserted automatically as you add states.

8. In the Component page, select **pizza** as the composite bag entity variable.

9. Click the menu <img src=./images/menu_icon.png"> in the **resolvePizza** state, then click **Add state** and select a **Send Message Component**.

10. Fill in the properties as below and click **[Insert]**

        | Property | Value |
        | ----------- | ----------------- |
        | Name | confirmMessage |
        | Description | Confirmation Messages |
		
11. In the Component page of the property inspector, reference the confirmation message resource bundle:


	`${rb('cbe.confirmation.message')}`

	<p>When you're done, the flow should look like this:


<!-- 
====================================================================
= TEST OUT THE FLOW                                                =
====================================================================
-->
### Test Out The Flow
<p>Now we're going to test out the flow so far.


1. Open the Skill Tester.
	<img src="./images/Preview.png" width="500px">



2. Enter _Order pizza_. Then complete the order by selecting the pizza type and size
	<img src="./images/ODA-Bot-Avatar.gif" width="50px">


3. Note the routing from the Main Flow to the **intent.reg.order** flow and the subsequent traversal from the **startPizzaOrder** state to the **confirmMessage** state.

<img src="./images/ODA-Bot-Avatar.gif" width="50px">


4. Click **Reset** and then close the Skill Tester.

In this step, you learned how to create an intent event flow that references a skill-level variable and resource bundles.



<!-- 
====================================================================
= CALL ANOTHER FLOW                                                =
====================================================================
-->
## Call Another Flow
To handle the situation where a user decides to cancel their order before it is sent, you're going to branch the conversation to the pre-existing cancel order flow, **intent.reg.cancelOrder** (accessed by clicking **Flows > intent.reg.cancelOrder** in the left navbar). This simple flow outputs _"All right, you can peacefully forget about your pizza."_ using a Send Message state that references the **pizza.messages.cancelOrder** resource bundle.	

<img src="./images/ODA-Bot-Avatar.gif" width="50px">


<p>If you were writing the dialog definition in YAML instead of the Visual Flow Designer, the order pizza and cancel pizza flows would be part of a monolithic block of code. Because of the modular approach afforded by the Visual Flow Designer, you create separate flows which you link together.

1. Hover over the next transition line between the **resolvePizza** and the **confirmMessage** states. Then click <img src="./images/add_state_to_transition.icon" > to add a state to the next transition.

	<img src="./images/ODA-Bot-Avatar.gif" width="50px">




2. From the Add State dialog, select the Resolve Entity template by selecting **User Messaging > Resolve Entities > Resolve Entity**, or by entering resolve entity in the Search field.

	<img src="./images/ODA-Bot-Avatar.gif" width="50px">


	- Set the properties as follows
	
	 | Property | Value |
	 | ----------- | ----------------- |
    	 | Name | confirmSelection |
	 | Description | Resolves the confirmation Entity |
	

	<img src="./images/ODA-Bot-Avatar.gif" width="50px">


3. In the Component page of the property inspector for the **confirmSelection** state, choose **confirmation** as the flow-scope variable.

	<img src="./images/ODA-Bot-Avatar.gif" width="50px">



4. Hover over the next transition line that's between the **confirmSelection** state and the **confirmMessage** state, then click <img src="./images/add_state_to_transition.icon.png">


5. Choose **Flow Control**, then choose **Invoke Flow**.  Define the properties as below and click **[Insert]** to add the component to the flow.

 	| Property | Value |
	| ----------- | ----------------- |
	| Name | cancelCurrentOrder |
	| Description | Calls intent.reg.cancelOrder |
	



6. In Component page, select **intent.reg.cancelOrder**, the cancel order flow, as the flow that will be called when the dialog transitions to this state. If you were passing values like pizza size and pizza toppings to the cancel order flow, then you'd also define input parameters for this component.
	<img src="./images/ODA-Bot-Avatar.gif" width="50px">




7. Hover over the next transition line between the **confirmSelection** and **cancelCurrentOrder** states and click <img src="./images/add_state_to_transition.icon.png"> once again to add another component.


8. Choose **Flow Control**, then **Switch**. Define the properties as below and click **[Insert]** to add the component to the flow.

	| Property | Value |
	| ----------- | ----------------- |
  	| Name | routeSelection |
	| Description | Branches Conversation |
	
	<img src="./images/ODA-Bot-Avatar.gif" width="50px">


9. In the Component page of the property inspector for the routeSelection state

        - Set the Request Body – **Expression** switch to **“ON”**
	
        - Reference the **Yes | No** value from the confirmation variable that you created for this flow. To do this, paste the following expression into the field
	
	`${confirmation.value.confirmation.primaryLanguageValue}`


	<img src="./images/ODA-Bot-Avatar.gif" width="50px">

        - Click outside the field to accept the input.


10. Navigate to the **“Transitions”** tab at the top of the **routeSelection** component Palette.


11. Select **End Flow (implicit)** as the Next Transition.

	<img src="./images/ODA-Bot-Avatar.gif" width="50px">


12. Click on the <img src="./images/add_action_icon.png"> next to the “Action” to enter the appropriate transition when the value of the Confirmation variable is **YES**.
	
        - For Action Name, enter **Yes**
	- For Transition to, select **ConfirmMessage**.
	- Click Save <img src="./images/save.png">.
	
13. Click on the <img src="./images/add_action_icon.png"> again to enter transition when the value of the Confirmation variable is **NO**

        - For Action Name, enter **No**
	- For Transition to, select **ConfirmMessage**.
	- Click Save <img src="./images/save.png">.

	<img src="./images/ODA-Bot-Avatar.gif" width="50px">

	
	At this point, the flow should look like this. Note the No and Yes transitions branching from the **routeSelection** state. Note also that there's a next transition wired to the **cancelCurrentOrder** state

	
        <img src="./images/ODA-Bot-Avatar.gif" width="50px">


14. To prevent a transition from the **cancelCurrentOrder** state to the **confirmMessage** state:

        - Open the property inspector for the **cancelCurrentOrder** state.
	- Select the Transitions page..
	- Select **End flow (implicit)** as the next transition.
	
	When you're done, the flow should look like this:

	<img src="./images/ODA-Bot-Avatar.gif" width="50px">


	This completes this flow..



<!-- 
====================================================================
= TEST OUT THE FLOW                                                =
====================================================================
-->
### Test Out The Flow
1. Repeat the prior conversation in the Skill Tester for both the Yes and No options.

        - Open the Skill Tester.
	  <img src="./images/Preview.png" width="50px">

        - Enter _**Order pizza**_. Then complete the order by selecting the pizza type and size
	
        - When you reach the **confirmSelection** state, choose **Yes**.
	 <img src="./images/Preview.png" width="50px">

	The conversation routes from the Main Flow to the **intent.reg.OrderPizza** flow and culminates in the **confirmMessage** state.
	
	- Reset the conversation and enter _**Order pizza**_ again. Select the pizza type and size
	
        - When you reach the **confirmSelection** state, choose **No**.

		<img src="./images/Preview.png" width="50px">

	The conversation gets routed from the Main Flow, through the **intent_reg.orderPizza** flow, and then finally to the **intent.reg.cancelOrderflow** because of the “No” answer given for the confirmSelection state.
	


            

<!-- 
====================================================================
= Respond to the feedback Sentiment                                =
====================================================================
-->
## Respond to the feedback Sentiment
Let’s now extend the basic NLP functionality of the Digital Assistant to take advantage of the OCI AI services defined earlier

1. Navigate to the Flow Designer page for your skill.
	
2. Click **[+ Add Flow]** button to add a new intent-driven flow to the skill using the following properties.

        | Property | Value |
        | ----------- | ----------------- |
        | Name | collect.feedback |
        | Description | Flow to collect customer feedback on Pizza & service |
        | Intent Name | pizza.reg.giveFeedback |

	<img src="./images/ODA-Bot-Avatar.gif" width="50px">

   - Check **“open created flow afterwards”** and click **[Create]**
			
3. Add an **“Ask Question”** component as the Start State for the flow.

        | Property | Value |
        | ----------- | ----------------- |
        | Name | PromptForFeedback |
        | Description | Prompt for customer feedback |
		
        | | | |
        | :-----------: | :-----------: | :-----------------: |
        | <img src="./images/add_start_state.png"> | <img src="./images/ArrowGrey.gif"> | <img src="./images/ODA-Bot-Avatar.gif" width="50px"></td> |

4. Enter the Question text using the predefined resource bundle

`${rb('PromptForFeedback.message')}`

5. Click the Variable **[Create v]** button to create a flow variable to store the user’s feedback.

        | Property | Value |
        | ----------- | ----------------- |
        | Name | feedbackText |
        | Description | Variable to hold user feedback |
        | Variable Type | String |
		

| | | |
| <img src="./images/add_start_state.png"> | <img src="./images/ArrowGrey.gif"> | <img src="./images/ODA-Bot-Avatar.gif" width="50px"></td> |
		
        - Click **[Apply]** to create the “feedbackText” variable

6. Call Language Service via REST Connector

   - Add the **“Call REST Service”** component as the **“Next”** state in the flow

     Note: You can use the Search filter to quickly find the REST component.

     | Property | Value |
     | ----------- | ----------------- |
     | Name | CallSentimentService |
     | Description | Calling OCI Language API |
				

    - Click **[Insert]** to add the component to the flow

    | | | |
    | <img src="./images/add_start_state.png"> | <img src="./images/ArrowGrey.gif"> | <img src="./images/ODA-Bot-Avatar.gif" width="50px"></td> |

				
7. In the **“CallSentimentService”** component palette select the **“DetectSentiment”** REST service from the dropdown list.

   <img src="./images/CallSentimentService.png" width="200px">


8. Select the POST method from the Methods dropdown list.

9. As we want to pass the feedback entered in the previous step, update the Request Body property.

   - Set the Request Body – **Expression** switch to **“ON”**
			
   - Paste the following into the Request Body Field

     `{ "documents": [{  "key": "pizzaFB",  "text": "${FeedbackText.value}" }] }`
			
   - Click outside the field to accept the input.
			
10. Confirm that the **“Response Mode”** is set to **“Use Actual REST API Response”**
	
11. Click the **[Create]** button to create a “Flow Scope” variable in which to store the Response from the AI REST service.

        | Property | Value |
        | ----------- | ----------------- |
        | Name | AIServicePayload |
        | Description | Variable to hold AI Service Response |
        | Variable Type | MAP |
		
    - Click **[Apply]** to create the variable.
			
      **Note:** if the Result variable field is not visible use the scroll bar within the component palette to bring it into view.
	

12. Navigate to the **“Transitions”** tab at the top of the **“CallSentimentService”** component Palette.
	
13. Click on the <img src="./images/add_action_icon.png"> next to the “Action” to enter the appropriate Transitions based on the outcome of the REST Service call.

    **Note:**  HTTP Status codes 2xx map to “Success”, 4xx|5xx map to “failure” in the defined REST Component


14. Select **“failure”** from the **“Action Name”** dropdown list.

    <img src="./images/Failure_Action_Name.png" width="200px">


15. Select **“Add State”** from the **“Transition To”** dropdown list.

    <img src="./images/Failure_Action_Transition.png" width="200px">

16. Add a **“Send Message”** component to the flow to indicate an invalid REST service call.

	| Property | Value |
	| ----------- | ----------------- |
	| Name | RequestFailed |
	| Description | Unsuccessful REST Request Message |
		

	<img src="./images/Add_State_RequestFailed.png" width="500px">

	Click **[Insert]** button to add component to the flow.
	

17. Select the **“RequestFailed”** state in the flow to launch the component’s property Palette and navigate to the component Tab.
	

18. Enter the following Resource Bundle reference for the output message if the REST service fails.

    `${rb('requestFailed.message')}`

	
19. Select the **"CallSentimentService"** state in the Flow to expose its component palette. Navigate to the **“Transitions”** tab.
	
20. Click on the <img src="./images/add_action_icon.png"> again (next to Action) to enter another Transition based on the outcome of the REST Service call.
	
21. Select **“success”** from the **“Action Name”** dropdown list.
	
22. Select **“Add State”** from the **“Transition To”** dropdown list.  This time add the invoke flow template.

	| Property | Value |
	| ----------- | ----------------- |
	| Name | RespondToFeedback |
	| Description | Calling a predefined flow to respond to the sentiment within the user’s feedback |
		

	<img src="./images/Add_State_RespondToFeedback.png" width="500px">

23. Select the **“RespondToFeedback”** state in the flow and navigate to the component tab
	
24. Select the **“respond.to.feedback”** flow from the choice of available flows in the dropdown list.
	
25. Click on the <img src="./images/add_action_icon.png"> next to the **“Input Parameters”** to specify the data to be passed to the **“respond.to.feedback”** flow.  In this case we want to pass in the ‘sentiment’ extracted from the feedback previously entered.
	
26. Choose the **"sentiment"** parameter from the dropdown list.
	
27. Paste the following ${freemarker expression} to retrieve the Sentiment from the Payload that was returned in the previous **"CallSentimentService"** state.  Remember the payload was stored in the **"AIServicePayload"** variable.

	`${AIServicePayload.value.responsePayload.documents[0].documentSentiment}`

	<img src="./images/Input_Parameter_Sentiment.png" width="200px">

	- Click the <img src="./images/Save.png"> (tick) to save the parameter definition.
			
	Now to associate the flow with the user’s intent to give feedback on the Pizza and/or service.

	
28. Select **“Main Flow”** in the main list of flows and click on the <img src="./images/add_action_icon.png"> next to the **“Intent Events”** to map the correct intent to the **“collect.feedback”**.

    | Property | Value |
    | ----------- | ----------------- |
    | Intent Name | pizza.reg.giveFeedback |
    | Mapped Flow | collect.feedback |
		

    <img src="./images/Create_Event_Handler_GiveFeedback.png" width="200px">

    - Click **[Create]**
			

This completes the development of the feedback flow that captures the sentiment from the user’s feedback.

<!-- 
====================================================================
= TEST OUT THE FLOW                                                =
====================================================================
-->

### Test Out The Flow
1. Test the flow by testing the intent in the conversation preview.
	

2. Enter _**“I want to give some feedback”**_ to kick off the intent
	

3. Enter some feedback that reflects a Positive or Negative response to the Pizza order.
   Eg.
	- The delivery was very slow, and the pizza was cold
	- The pizza was piping hot and tasted great.
	- I have had better pizzas
	- Awesome pizza!!
	- I will order this again
	- Worst pizza ever!

<!-- 
====================================================================
= Respond to the feedback Sentiment                                =
====================================================================
-->

## Respond to the user if their utterance is in a Foreign Language
<p>While the Oracle Digital Assistant implements a sophisticated multi-lingual NLP engine, that natively supports a range of languages, there is likely to be the need to respond accordingly if the language used is not one supported natively.  That is, notifying the user that the bot is not able to understand the given language.	
<p>To detect which language was entered, and subsequently print it out in the message to the user, the Digital Assistant can again utilise the OCI AI-Language APIs.  In this case using the **“DetectLanguage”** API.
<p>For this Tutorial, we will replace the default **“intent.unresolved”** flow, with a custom version that calls the AI service to determine the language of any utterance the bot was unable to understand.
<p>This will follow a similar process to the **Detect Sentiment** flow above.

1. Navigate to the Flow Designer page for your skill.
	
2. Click **[+ Add Flow]** button to add a new flow to the skill using the following properties.
   | Property | Value |
   | ----------- | ----------------- |
   | Name | intent.unresolved.with.language.check |
   | Description | Customised Unresolved Intent flow that calls the OCI AI Select Language service |
   | Intent Name | Not Defined |
		

   -  Check **“open created flow afterwards”** and click **[Create]**

			
3. As we want to check the language, as soon as the Flow is called, add a **“Call REST Service”** component as the Start State for the flow.
   | Property | Value |
   | ----------- | ----------------- |
   | Name | CallLanguageService |
   | Description | Call OCI Language API – Detect Language |

   - Click **[Insert]** to update the flow
			
4. In the **“CallLanguageService”** component palette select the **“DetectLanguage”** REST service from the dropdown list.

	
5. Select the **POST** method from the Methods dropdown list.

	
6. As we want to pass the utterance that was unresolved to the Language API, update the REST service request to include a reference to the user’s latest input

	- Set the Request Body – **Expression** switch to **“ON”**

	- Paste the following into the Request Body Field
				`{ "text": "${system.message.messagePayload.text}" }`
			
	- Click outside the field to accept the input.

7. Confirm that the **“Response Mode”** is set to **“Use Actual REST API Response"**

	
8. As Flow Scoped variables are only available within the flow in which they were created, create another “Flow” variable in which to store the Response from the AI REST service.  As these need only be unique within the current flow, we can use the same name as before.

	| Property | Value |
	| ----------- | ----------------- |
	| Name | AIServicePayload |
	| Description | Variable to hold AI Service Response |
	| Variable Type | MAP |


    - Click **[Apply]** to create the variable
			
9. Navigate to the **“Transitions”** tab at the top of the **“CallLanguageService”** component Palette.


10. Click on the <img src="./images/add_action_icon.png"> next to the **“Action”** to enter the appropriate Transitions based on the outcome of the REST Service call.

	
11. Select **“failure”** from the **“Action Name”** dropdown list.

	
12. Select **“Add State”** from the **“Transition To”** dropdown list.

	
13. Add a **“Send Message”** component to the flow to indicate an invalid REST service call. Again because it is scoped to the specific flow we can reuse the component name.
 
    | Property | Value |
    | ----------- | ----------------- |
    | Name | RequestFailed |
    | Description | Unsuccessful REST Request Message |

    - Click **[Insert]** button to add component to the flow.

14. Select the **“RequestFailed”** state in the flow to launch the component’s property Palette and navigate to the component Tab.

15. Again, enter the failed message Resource Bundle reference to print the appropriate output message, if the REST service fails.

		`${rb('requestFailed.message')}`

	
16. Select the **“CallLanguageService”** state in the Flow to expose its component palette and navigate to the **“Transitions”** tab.

	
17. Click on the <img src="./images/add_action_icon.png"> next to the “Action” to enter another Transition based on the outcome of the REST Service call.

	
18. Select **“success”** from the **“Action Name”** dropdown list.

	
19. Select **“Add State”** from the **“Transition To”** dropdown list.  This time add an **“Invoke flow”** component.
 
 | Property | Value |
 | ----------- | ----------------- |
 | Name | RespondToLanguage |
 | Description | Calling a predefined flow to respond to the language used in the given utterance |
		

20. Select the **“respond.to.language”** flow from the choice of available flows in the dropdown list.

21. Click on the <img src="./images/add_action_icon.png"> next to the **“Input Parameters”** to specify the data to be passed to this flow.  In this case we want to pass in the ‘language’ that was detected in the utterance.

22. Choose the **"language"** parameter from the dropdown list.

23. Paste the following ${freemarker expression} to retrieve the language detected in the  utterance.

        `${AIServicePayload.value.responsePayload.languages[0].name}`

	- Click the <img src="./images/Save.png"> ("Tick") to save the parameter definition
			
24. To ensure this flow is executed if the user enters an utterance in a language for which the Bot has not been trained.

25. Select **“Main Flow”** in the main list of flows and click on the “Unresolved Intent” entry under “Built-In Event” to expose the edit pencil icon.
			
	  <img src="./images/Map_Event_Unresolved.png" width="500px">

26. Select **“intent.unresolved.with.language.check”** from the list of available flows and click **[Apply]**

	<img src="./images/Modify_Event_Mapping_Unresolved.png" width="500px">

This completes the development of the custom Unresolved Intent flow that utilises the OIC AI-Language API to detect if the language used is one that is supported by the Bot.


<!-- 
====================================================================
= TEST OUT THE FLOW                                                =
====================================================================
-->
### Test Out The Flow
1. Train the Skill using “Trainer tm” and once trained open the skill in the Bot Preview
		<img src="./images/Train.png" width="500px">

2. Enter a phrase in English for which the Bot has not been trained and hence would call the unresolved intent.

   - **_“I want to order a Hamburger”_**
		
3. Now try the same phrase in other languages

   - **“Chcę zamówić hamburgera” (Polish)**
   - **“햄버거를 주문하고 싶어요” (Korean)**
   - **“Vreau să comand un hamburger” (Romanian)**
		

This ends the Hands-on lab!

In this short session you have expanded on the “Pizza Order” bot and would have seen how the Digital Assistant can easily utilise the sophisticated capabilities presented by the OCI AI services.


         
 <!-- 
====================================================================
= Learn More                                                       =
====================================================================
-->           
<!-- Include the "Learn More" section only in a standalone tutorial or in the final lab or tutorial in a series (not in a learning path) -->
			
## Learn More
 - <a href="https://docs.oracle.com/pls/topic/lookup?ctx=en/cloud/paas/digital-assistant&id=datvf-index">Create a Dialog Flow with the Oracle Visual Flow Designer</a><li><a href="https://docs.oracle.com/en/cloud/paas/digital-assistant/tutorial-intents/index.html">Best Practices for Building and Training Intents</a>
 - <a href="http://docs.oracle.com/pls/topic/lookup?ctx=en/cloud/paas/digital-assistant&id=DACUA-GUID-386AB33B-C131-4A0A-9138-6732AE841BD8" target="_blank">Using Oracle Digital Assistant</a>



<!-- 
====================================================================
= FOOTER                                                   =
====================================================================
-->  
<!-- DO NOT ALTER THE CODE BELOW EXCEPT FOR THE METADATA SECTION -->
<hr>
<div>
	<a href="#copyright-information" role="button" data-toggle="collapse" aria-expanded="false" class="collapsed" aria-controls="copyright-information" id="copyright-information-btn">Title and Copyright Information</a>
</div>
<div class="collapse" id="copyright-information" aria-expanded="false">
<!-- Metadata Begin -->
	<p>Deliver Immersive Conversational User Experiences with OCI AI Services
	<p>F70558-01
	<p class="date">September 2022 <!-- Publication date -->
	<p>Copyright&nbsp;&copy;&nbsp;2022, Oracle&nbsp;and/or&nbsp;its&nbsp;affiliates.&nbsp;
	<p>Shows how use Oracle Cloud Infrastructure's AI services within a digital assistant.
<!-- Metadata End -->
<div>
	<p>This software and related documentation are provided under a license agreement containing restrictions on use and disclosure and are protected by intellectual property laws. Except as expressly permitted in your license agreement or allowed by law, you may not use, copy, reproduce, translate, broadcast, modify, license, transmit, distribute, exhibit, perform, publish, or display any part, in any form, or by any means. Reverse engineering, disassembly, or decompilation of this software, unless required by law for interoperability, is prohibited.
</div>
<div>
	<p>If this is software or related documentation that is delivered to the U.S. Government or anyone licensing it on behalf of the U.S. Government, then the following notice is applicable:
	<p>U.S. GOVERNMENT END USERS: Oracle programs (including any operating system, integrated software, any programs embedded, installed or activated on delivered hardware, and modifications of such programs) and Oracle computer documentation or other Oracle data delivered to or accessed by U.S. Government end users are "commercial computer software" or "commercial computer software documentation" pursuant to the applicable Federal Acquisition Regulation and agency-specific supplemental regulations. As such, the use, reproduction, duplication, release, display, disclosure, modification, preparation of derivative works, and/or adaptation of i) Oracle programs (including any operating system, integrated software, any programs embedded, installed or activated on delivered hardware, and modifications of such programs), ii) Oracle computer documentation and/or iii) other Oracle data, is subject to the rights and limitations specified in the license contained in the applicable contract. The terms governing the U.S. Government's use of Oracle cloud services are defined by the applicable contract for such services. No other rights are granted to the U.S. Government.
</div>
<div>
	<p>This software or hardware is developed for general use in a variety of information management applications. It is not developed or intended for use in any inherently dangerous applications, including applications that may create a risk of personal injury. If you use this software or hardware in dangerous applications, then you shall be responsible to take all appropriate fail-safe, backup, redundancy, and other measures to ensure its safe use. Oracle Corporation and its affiliates disclaim any liability for any damages caused by use of this software or hardware in dangerous applications.
</div>
<div>
	<p>Oracle and Java are registered trademarks of Oracle and/or its affiliates. Other names may be trademarks of their respective owners.
	<p>Intel and Intel Inside are trademarks or registered trademarks of Intel Corporation. All SPARC trademarks are used under license and are trademarks or registered trademarks of SPARC International, Inc. AMD, Epyc, and the AMD logo are trademarks or registered trademarks of Advanced Micro Devices. UNIX is a registered trademark of The Open Group.
</div>
<div>
	<p>This software or hardware and documentation may provide access to or information about content, products, and services from third parties. Oracle Corporation and its affiliates are not responsible for and expressly disclaim all warranties of any kind with respect to third-party content, products, and services unless otherwise set forth in an applicable agreement between you and Oracle. Oracle Corporation and its affiliates will not be responsible for any loss, costs, or damages incurred due to your access to or use of third-party content, products, or services, except as set forth in an applicable agreement between you and Oracle.
</div>
</body>
</html>
