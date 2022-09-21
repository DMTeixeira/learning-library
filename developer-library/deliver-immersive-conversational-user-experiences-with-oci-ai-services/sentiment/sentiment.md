## Respond to the feedback Sentiment
Let’s now extend the basic NLP functionality of the Digital Assistant to take advantage of the OCI AI services defined earlier

1. Navigate to the Flow Designer page for your skill.
	
2. Click **[+ Add Flow]** button to add a new intent-driven flow to the skill using the following properties.

| Property | Value |
| ----------- | ----------------- |
| Name | collect.feedback |
| Description | Flow to collect customer feedback on Pizza & service |
| Intent Name | pizza.reg.giveFeedback |

![](images/sentiment1.png =50%x*  "")

- Check **“open created flow afterwards”** and click **[Create]**
			

3. Add an **“Ask Question”** component as the Start State for the flow.

| Property | Value |
| ----------- | ----------------- |
| Name | PromptForFeedback |
| Description | Prompt for customer feedback |
		

![](images/sentiment2.JPG =60%x*  "")

4. Enter the Question text using the predefined resource bundle

```
${rb('PromptForFeedback.message')}
```

5. Click the Variable **[Create v]** button to create a flow variable to store the user’s feedback.

| Property | Value |
| ----------- | ----------------- |
| Name | feedbackText |
| Description | Variable to hold user feedback |
| Variable Type | String |
		

![](images/sentiment3.JPG =60%x*  "")
		
- Click **[Apply]** to create the “feedbackText” variable

6. Call Language Service via REST Connector

   - Add the **“Call REST Service”** component as the **“Next”** state in the flow

     **Note:** You can use the Search filter to quickly find the REST component.

| Property | Value |
| ----------- | ----------------- |
| Name | CallSentimentService |
| Description | Calling OCI Language API |
				

 - Click **[Insert]** to add the component to the flow

![](images/sentiment4.JPG =60%x*  "")

				
7. In the **“CallSentimentService”** component palette select the **“DetectSentiment”** REST service from the dropdown list.

![](images/sentiment5.png =60%x*  "")


8. Select the POST method from the Methods dropdown list.

9. As we want to pass the feedback entered in the previous step, update the Request Body property.

   - Set the Request Body – **Expression** switch to **“ON”**
			
   - Paste the following into the Request Body Field

     ```
     { "documents": [{  "key": "pizzaFB",  "text": "${FeedbackText.value}" }] }
     ```
			
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

    ![](images/sentiment6.png =60%x*  "")


15. Select **“Add State”** from the **“Transition To”** dropdown list.

   ![](images/sentiment7.png =60%x*  "")

16. Add a **“Send Message”** component to the flow to indicate an invalid REST service call.

| Property | Value |
| ----------- | ----------------- |
| Name | RequestFailed |
| Description | Unsuccessful REST Request Message |
		

![](images/sentiment8.png =60%x*  "")

Click **[Insert]** button to add component to the flow.
	

17. Select the **“RequestFailed”** state in the flow to launch the component’s property Palette and navigate to the component Tab.
	

18. Enter the following Resource Bundle reference for the output message if the REST service fails.

    ```
    ${rb('requestFailed.message')}
    ```

	
19. Select the **"CallSentimentService"** state in the Flow to expose its component palette. Navigate to the **“Transitions”** tab.
	
20. Click on the <img src="./images/add_action_icon.png"> again (next to Action) to enter another Transition based on the outcome of the REST Service call.
	
21. Select **“success”** from the **“Action Name”** dropdown list.
	
22. Select **“Add State”** from the **“Transition To”** dropdown list.  This time add the invoke flow template.

| Property | Value |
| ----------- | ----------------- |
| Name | RespondToFeedback |
| Description | Calling a predefined flow to respond to the sentiment within the user’s feedback |
		

![](images/sentiment9.png =60%x*  "")

23. Select the **“RespondToFeedback”** state in the flow and navigate to the component tab
	
24. Select the **“respond.to.feedback”** flow from the choice of available flows in the dropdown list.
	
25. Click on the <img src="./images/add_action_icon.png"> next to the **“Input Parameters”** to specify the data to be passed to the **“respond.to.feedback”** flow.  In this case we want to pass in the ‘sentiment’ extracted from the feedback previously entered.
	
26. Choose the **"sentiment"** parameter from the dropdown list.
	
27. Paste the following ${freemarker expression} to retrieve the Sentiment from the Payload that was returned in the previous **"CallSentimentService"** state.  Remember the payload was stored in the **"AIServicePayload"** variable.

	```
    ${AIServicePayload.value.responsePayload.documents[0].documentSentiment}
    ```
![](images/sentiment10.png =60%x*  "")

	- Click the <img src="./images/Save.png"> (tick) to save the parameter definition.
			
	Now to associate the flow with the user’s intent to give feedback on the Pizza and/or service.

	
28. Select **“Main Flow”** in the main list of flows and click on the <img src="./images/add_action_icon.png"> next to the **“Intent Events”** to map the correct intent to the **“collect.feedback”**.

| Property | Value |
| ----------- | ----------------- |
| Intent Name | pizza.reg.giveFeedback |
| Mapped Flow | collect.feedback |
		

![](images/sentiment11.png =60%x*  "")

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