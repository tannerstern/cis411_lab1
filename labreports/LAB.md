# CIS411_Lab1 Lab Report: tannerstern
Course: Messiah College CIS 411, Spring 2020<br/>
Instructors: [Joel Worrall](https://github.com/tangollama) & [Trevor Bunch](https://github.com/trevordbunch)<br/>
Name: Tanner Stern<br/>
GitHub: [tannerstern](https://github.com/tannerstern)


# Step 0: Reviewing Architectural Patterns
See the [lecture / discussion](https://docs.google.com/presentation/d/1nUcy63FWPFYO3OJmERJpMjEtdaFtaIBbuUkpmNRVRas/edit#slide=id.g45345bd5ea_0_136) from CIS 411. You'll need to be familiar with the content from this lecture to complete this assignment.

Note: you are free to work with classmates on this assignment. _Good architecture is born out of collaboration - not reclusive mad-scientist behavior._ However, if you work with colleagues:

1. You must specifically note your collaborators by name at the top of your report.
2. You may not completely copy each others work (diagrams and descriptions, even if your solutions are identical).

# Step 1: MVC Architecture
Review the proposals for the Serve Central project. Let's imagine that the project has been granted (relatively) unlimited resources if they can deliver a version 1 release in 120 days. As a result, the team decides to implement an MVC architecture for its version 1 release, delivering functionality through a [responsive web application](https://en.wikipedia.org/wiki/Responsive_web_design). 

Based on the [this](https://docs.google.com/presentation/d/1UnU0xU0wF1l8pAB8trtLpdM0yuskx66jTFJzd64nsjU/edit#slide=id.g439b9c6866_2_53) and [this](https://docs.google.com/presentation/d/1-VZfAFoBVr6ijNepKAtRA7JoAQsV2Jlbf2l1WPDMhI0/edit) presentation:

1) Document two use cases of your choosing

| Use Case #1 | |
|---|---|
| Title | As a user, I need to be able to search for service opportunities near me. |
| Description / Steps | 1) The user provides access to their location (through GPS or manual input)<br/> 2) The user enters keywords on which to filter volunteer opportunities<br/> 3) The system determines which service opportunities are physically near the user and fit the search criteria<br/> 4) The user is presented with opportunities that match their criteria on a map |
| Primary Actor | Volunteer (User of the App) |
| Preconditions | 1) Events are loaded in the app<br/> 2) User knows their location |
| Postconditions | The user sees the map that shows relative location and distance between service opportunities |

| Use Case #2 | |
|---|---|
| Title | As a user, I want to be able to track the volunteer events I have participated in. |
| Description / Steps | 1) The user clicks on their profile link<br/> 2) The system loads the user's participation data<br/> 3) The system formats the user's data for viewing, organizes it by date and calculates  statistics <br/> 4) The user can view the data<br/> |
| Primary Actor | Volunteer (User of the App) |
| Preconditions | The user has participated (registered) for volunteer events |
| Postconditions | The user sees their participation data |


2) Highlight a [table](https://www.tablesgenerator.com/markdown_tables) of at least **four models, views, and controllers** needed to produce this project.

| Model | View | Controller |
|---|---|---|
| Volunteer | ProfilePage | ProfileController<sup>1</sup> |
| Event | EventPage | EventController<sup>2</sup> |
| User | LoginPage | UserController<sup>3</sup> |
| Location | EventMapPage | EventMapController<sup>4</sup> |
_Notes on functionality:<br/>
<sup>1</sup> Loads volunteer data, calculates statistics<br/>
<sup>2</sup> Loads event information, title, type<br/>
<sup>3</sup> Checks user input against DB, verifies registration<br/>
<sup>4</sup> Places events on map by location_

3) Generate and [embed](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet#images) at least one diagram of the interaction between an Actor from the Use Cases, and one set of Model(s), View(s), and Controller(s) from the proposed architecture, including all the related / necessary services (ex: data storage and retrieval, web servers, container tech, etc.)

![MVC Diagram](../assets/step1_diagram.png "MVC Diagram")

# Step 2: Enhancing an Architecture
After an initial release and a few months of operation, Serve Central encounters a tremendous growth opportunity to extend their service and provide a volunteer recuitment and management interface to __four__ of the primary volunteer entities in the United States. As such, a reevaluation of the architecture is required, one that allows:

1. Third party services to both input and retrieve data from the Serve Central model/datastore. (For instance, receiving volunteer opportunities from United Way chapters across the country.)
2. Building organization-specific interfaces on top of the Serve Central business and data logic. (For instance, allowing the registration services of Serve Central to be embedded in the website of local churches, [ah-la Stripe embedding](https://stripe.com/payments/elements).)

To support these objectives:
1. *What architectural patterns are appropriate? Justify your response, highlighting your presumed benefits / capabilties of your chosen architecture as well as as least one potential issue / adverse consequence of your choice.*<br/>
The architecture pattern I would recommend moving to for the increase in service is the **Layered Patter**. A layered pattern is not quite as tightly coupled as MVC, which allows for customizations at different layers. Third parties that need specific interfaces and data logic for accessing Serve Central data can have that autonomy. Where MVC would require all of those details to be defined in one place, a layered pattern provides some separation (containment) for different organizational approaches. One potential issue for moving in this direction is that managing third party organizational components within the structure becomes much more complex. Dependencies from one component in the layer may conflict with those of another.
2. *Using your preferred diagramming tool, generate a diagram of the new Serve Central architecture that supports these two new requirements.*<br/>

![Layer Diagram](../assets/step2_diagram.png "Layer Diagram")

# Step 3: Scaling an Architecture
18 months into the future, Serve Central is experiencing profound growth in the use of the service with more than 100k daily, active users and nearly 1M event registrations per month. As a result, the [Gates Foundation](https://www.gatesfoundation.org/) has funded a project to build and launch a mobile application aimed at encouraging peer-to-peer volunteer opportunity promotion and organization. 

In addition to building a new mobile application interface, the grant requires that the project prepare for the following future needs:

1. Consuming bursts of 10k+ new volunteer opportunities per hour with a latency of less than 15 seconds between submitting an opportunity and it's availability in the registration service.
2. Supporting a volunteer and event data store that will quickly exceed 50TB of data
3. Allowing authorized parties to issue queries that traverse the TB's of data stored in your datastore(s).
4. Enabling researchers to examine patterns of volunteer opportunities as a way of determining future grant investments.

*What archictural pattern(s) will you employ to support each of these needs? What will the benefits and consequences be? Why are changes needed at all? Justify your answers.*

The increased demand on the architecture will be too much for exisitng patterns to handle. They are not built to handle volume. To meet each of the above requirements, we will need to employ several patterns which augment the capabilities of their predacessors. First, to handle high-volumes of data and low-latency needs, we will use the **microservice pattern**. Microservice architecture allows for scalability not present in Layered or MVC: the whole program doesn't need to be fast, just the parts that are in high-demand. Whatever cloud provider that ServeCentral uses can increase resources for critial services as they are needed. The second pattern to implement is **Master-Slave** on ServeCentral's databases. With multiple parties requiring access to data there should be copies of the data so that research queries do not interfere with core-functionality. Furthermore, slave databases could be geographically distributed to provide faster access and greater redundancy.

# Extra Credit
1. Create and embed a comprehensive diagram of your final architecture (i.e. one that meets all the requirements of this lab, including Step 3).
2. Augment/improve the assignment. Suggest meaningful changes in the assignment and highlight those changes in the extra credit portion of your lab report.