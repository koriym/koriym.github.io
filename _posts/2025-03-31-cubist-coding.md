---
layout: post
title: "From \"As-Seen\" to Reconstructed Reality"
date: 2025-03-31 00:00:00 +0900
comments: true
categories: ["blog"]
tags:
- API
---

## Cubist Coding

*Where Picasso Meets API*

"Why can't our APIs fully express complex reality?"

Have you ever deeply considered this question? Many developers face this challenge and continuously search for better frameworks and patterns. But what if the fundamental issue isn't with technology but with our very "perception of reality"?

As programmers, we've consistently built "as-seen" worlds. We transform user actions into procedural endpoints and treat data models as simple mappings of the real world. This "as-seen" approach is intuitive but, as systems grow more complex, fails to capture the richness and possibilities of reality.

Interestingly, an attempt to break through these perceptual limitations occurred in the art world about 100 years ago.

In the early 20th century, Pablo Picasso and Georges Braque questioned the traditional perspective—the "as-seen" representation. The Cubism they pioneered wasn't merely a new painting style but a fundamentally new way of seeing and understanding reality—a method of breaking down subjects, viewing them simultaneously from multiple perspectives, and reconstructing them.

Similarly, today's software architecture stands at a turning point. Beyond traditional "as-seen" API design, we need a "Cubist" approach that decomposes the problem domain and reconstructs it with new perspectives. This essay explores the possibilities of a new programming paradigm inspired by art history.

## The Cubist Revolution in Art

Cubism arose from a simple yet profound insight: a single viewpoint cannot capture the totality of an object. By fragmenting forms, presenting multiple perspectives simultaneously, and reducing objects to essential geometric elements, Cubists revealed deep truths about their subjects that traditional representational art could never uncover.

The philosopher Maurice Merleau-Ponty's theory of perception offers a deeper understanding of this Cubist challenge. Merleau-Ponty conceived human perception not as a passive, camera-like process, but as an active, integrative experience through the body. According to him, when we see the world, our gaze constantly moves, connecting with touch and bodily sensations to grasp objects. This perceptual process, which Merleau-Ponty called "lived perspective," is precisely the "way we actually perceive" that the Cubists attempted to express.

Looking at Picasso's "Portrait of Ambroise Vollard" or Braque's "Violin and Candlestick," we see subjects that have been deconstructed, analyzed, and reconstructed into something beyond mere visual representation. This wasn't merely an aesthetic choice but an epistemological one.

Cubism asserted that understanding doesn't come from attachment to a single perspective, but emerges from the synthesis of multiple viewpoints. Here, "viewpoint" means not just physical observation position but the entire framework of interpretation. Showing the front and side of an object simultaneously means presenting different knowledge or interpretations of that object simultaneously. For instance, when depicting a musical instrument, its appearance, internal structure, sound, player's movement, and even its social significance are all expressed "simultaneously" on a single canvas.

This process of deconstruction and reconstruction was an attempt to capture the "essence" rather than the "appearance" of objects. Cubists first deconstructed the object, digested its components, and reconstructed them into a new wholeness to reach deeper understanding and expression. In this process, the concept of a single "correct" viewpoint was abandoned in favor of a layered synthesis of insights gained from diverse perspectives.

The same transformative tension exists in how we design software systems today.

## The Allure of "As-Seen" Programming

Most of today's APIs adhere to what I call "as-seen programming"—a design philosophy that reflects how we superficially recognize actions in the real world. Consider this common pattern:

```python
# Linear "as-seen" flow (pseudo-RPC REST)
def create_order():
    # Endpoint: POST /orders
    validate(data)           
    order_id = save_to_db(data)            
    return {"id": order_id, "status": "CREATED"}
    
def get_order(id):
    # Endpoint: GET /orders/{id}
    return db.find_order(id)
    
def process_payment(order_id, payment_info):
    # Endpoint: POST /orders/{id}/payments
    order = db.find_order(order_id)
    process(order, payment_info)
    return {"status": "SUCCESS"}
```

We dress remote procedure calls in REST costumes, but the underlying mental model remains procedural: mapping methods to endpoints, passing parameters, receiving results. This is the computational version of perspective drawing—a model that feels intuitive because it mimics the way we've been taught to see the world.

There are reasons beyond technical considerations for why this approach dominates software development. It perfectly aligns with how developers already think procedurally. Modern frameworks and tools are optimized for these route handler patterns, becoming the path of least resistance. Conway's Law further reinforces this tendency—teams build APIs that reflect their communication structure, leading to endpoint designs that reflect organizational boundaries rather than domain insights.

If an API is merely a remote function call, we've just stretched a monolith over a network rather than designing a distributed system. Yet we cling to this model because it delivers immediate satisfaction. The first endpoint is quickly completed, documentation is automatically written, and existing paradigms need not be challenged. The costs only become apparent as systems grow more brittle, lose adaptability, and resist evolution.

## The Cubist Alternative: Affordance-Centered Design

If asked to design an API, what would Picasso do? He would likely question the very concept of endpoints as fixed procedures. Instead, he would focus on *affordances*—properties of objects that communicate how to use them without explicit instructions.

Consider the difference between these two API responses:

Traditional:
```json
{
  "id": 42,
  "status": "PLACED",
  "total": 67.99
}
```

Affordance-centered:
```json
{
  "id": 42, 
  "status": "PLACED", 
  "total": 67.99,
  "_links": {
    "self": {"href": "/orders/42" },
    "https://example.com/rels/payment": { "href": "/orders/42/payments" },
    "https://example.com/rels/cancel": { "href": "/orders/42" }
  }
}
```

The second approach embodies Cubist principles. It presents multiple perspectives (data and possible actions) simultaneously and breaks down the concept of an order into its current state and possible state transitions. Most importantly, it reconstructs these elements to create a response that communicates not just what the order *is* but what it can *become*.

This transition from static data to dynamic possibilities aligns with how we experience the world in everyday life. Physical objects around us constantly present affordances (possibilities for action)—doorknobs invite rotation, buttons ask to be pressed. As philosopher Merleau-Ponty stated, our perception is not merely receiving information but an active dialogue with our environment. Our digital interfaces should follow the same principle, communicating not just data but "what can be done next," guiding users' natural exploration and action.

## Why Pseudo-RPC (For Now) Has Won

Despite its theoretical advantages, affordance-centered design is the exception, not the rule. In some ways, the victory of "as-seen" programming may have been inevitable.

Consider that the very act of assembling a URL and sending a request is itself an embodiment of "as-seen." There's a fundamental constraint that HTTP clients can't do anything else. When clients access servers, they ultimately must resort to the simple operation of "access this URL with this method." This is similar to how Picasso's complex, multifaceted perspectives ultimately had to be expressed on a two-dimensional canvas.

However, from a Cubist perspective, the real difference lies in the preceding stage. While traditional APIs map "as-seen" operations directly to endpoints, affordance-centered design first decomposes the problem domain into elements and reconstructs them in new ways. Even though clients ultimately execute HTTP requests, the choices and understanding of those requests are fundamentally different.

Nevertheless, development speed bias favors the short-term "as-seen" approach. RPC-style APIs can be rapidly deployed, easily documented, and immediately understood by other developers. The costs accumulate gradually: increased coupling, fragile client dependencies, and difficulty evolving systems independently. But when quarterly goals loom and feature backlogs grow, these costs are easy to ignore.

Environmental pressures further reinforce this bias. Business metrics reward shipping features, not evaluating architectural sustainability. The tooling ecosystem for hypermedia and affordance-centered development remains immature compared to robust frameworks supporting conventional patterns.

Perhaps most importantly, there are psychological barriers to overcome. The developer's mental model tends toward immediate actions rather than potential state transitions. Our incentives point there, so we optimize for the first 6 months of development rather than the next 6 years of evolution. However, even if we ultimately arrive at the form of an HTTP request, the process of decomposing and reconstructing the problem on the way there creates the essential difference.

## Beyond APIs: The Affordance Revolution

The principles of affordance-centered design extend far beyond basic REST APIs. Consider how this approach transforms UI development:

```jsx
// Affordance-centered component
<Order id={42}>  
  {(order, actions) => (
    <Card>
      <OrderSummary data={order} />
      
      {actions.canPay && (
        <PaymentForm 
          onSubmit={actions.pay} 
          options={actions.paymentMethods} 
        />
      )}
      
      {actions.canCancel && (
        <Button 
          onClick={actions.cancel}
          availableUntil={actions.cancelDeadline}
        >Cancel</Button>
      )}
    </Card>
  )}  
</Order>
```

Here, the component renders not just data but a landscape of possibilities based on current state and available transitions. The UI becomes not a static view of data but a dynamic reflection of system capabilities.

This pattern extends to data engineering, where event-sourced systems can maintain multiple simultaneous views of the same entity:

```scala
// Event-sourced system with temporal perspective
val orderTrajectory = events
  .filter(_.entityType == "Order")
  .filter(_.entityId == 42)
  .groupByKey(e => e.timestamp.toLocalDate)
  .aggregate(OrderStateProjection)
```

Like a Cubist painting showing multiple facets of a face simultaneously, this code reveals the evolution of an order over time, providing insights that a single snapshot could never deliver.

## Patterns of Cognitive Revolution: From "As-Seen" to Reconstruction

"As-seen" programming isn't merely a technical accident but a natural manifestation of human cognition. We always start from "as-seen" because it's natural as an initial stage of cognition. But looking back at the intellectual history of humanity, the same pattern repeats across domains: starting with direct observation, then decomposing objects and reconstructing them to reach new understanding.

Consider the development of astronomy. Ancient astronomers began with "as-seen" observations that the sun, moon, and stars revolve around Earth—the geocentric model (Ptolemaic system). However, Copernicus and Galileo deconstructed and reconstructed the same observational data in a fundamentally different way. They reinterpreted "as-seen" phenomena from the revolutionary perspective that Earth revolves around the sun. This is a classic example of cognitive reconstruction, a "Cubism of astronomy." By going beyond visible phenomena to fundamentally reconceptualize the structure of the cosmos, they reached a deeper understanding.

If affordance-centered design is so powerful, why not start there? The answer lies in the natural evolution of human cognition and learning. Jean Piaget's developmental psychology research suggests that we naturally evolve from concrete procedures to abstract models, not the other way around. We must crawl before we walk, and walk before we run.

This natural cognitive trajectory maps perfectly to the evolution of API design:

First, we mirror directly observed reality with RPC-style APIs, mapping functions directly to network calls—the "as-seen" stage. Next, as we discover the constraints of distributed systems, we recognize the need to decompose the problem domain into more fundamental elements. Finally, we reconstruct elements through resource orientation and hypermedia to build more adaptable, evolvable systems—the "Cubist" stage.

This progression isn't a failure but a necessary journey of cognitive development. The challenge isn't to skip steps but to recognize when we're ready for the next one. The pioneers of Cubism also fully understood traditional perspective before breaking it.

## Why Affordances Matter Now

We stand at a technological inflection point where affordance-centered design isn't just theoretically superior but becoming practically essential. Three converging trends make this approach increasingly valuable:

First, AI-driven interfaces are transforming how we interact with our systems. Large language models naturally navigate hypermedia, follow links, and understand possible actions without the fragile client-side logic that human-written code requires. APIs rich in affordances provide the perfect foundation for AI agents to operate effectively.

Second, event-rich ecosystems like Kafka and Kinesis have become backbone infrastructure in modern architectures. These systems naturally support the state transition models that underpin affordance-centered design, making implementation more feasible than ever. For example, in an order system, by subscribing to event streams like "OrderCreated," "PaymentProcessed," and "OrderShipped," you can dynamically bind the current state of the order entity with corresponding affordances (payment, cancellation, shipment tracking, etc.).

Finally, the reality of distributed teams developing in parallel demands decoupled evolution. When teams across time zones and continents need to build consistent systems without constant synchronization, affordance-rich interfaces provide the necessary flexibility.

The characteristics of these systems—self-documentation through discoverable capabilities, graceful degradation as components evolve asynchronously, emergent behavior through composition—align perfectly with the needs of modern software development.

## From Canvas to Code

Just as Cubism liberated artists from the constraints of single-point perspective, affordance-centered design liberates developers from the limitations of procedural-oriented thinking. It invites us to see our systems not just as collections of endpoints but as landscapes of possibility.

Picasso once said, "I don't paint things as I see them but as I think them." Perhaps it's time we too built systems not merely as we use them but as we understand them—as dynamic, multifaceted entities with the capacity to evolve and transform over time.

Let's not just build APIs—let's create landscapes of possibility.
