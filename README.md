# 700110-2122-Steering

## Introduction

The purpose of this lab is to introduce you to simple movement and steering behaviours.

## 1. Look at the code and explore the existing functionality

Run the code, explore the interface. 

Note the Agent class that simply stores a position and a velocity.

```cpp
class Agent
{
    private:
        glm::vec2 m_Position;
        glm::vec2 m_Velocity;
    public:
        Agent(const glm::vec2 & pPosition, const glm::vec2& pVelocity) : m_Position(pPosition), m_Velocity(pVelocity)
        {
        
        }

        glm::vec2 Position() { return m_Position; }
        glm::vec2 Velocity() { return m_Velocity; }
};

Agent agent = Agent(glm::vec2(0, 0), glm::vec2(0, 10));
```

It is worth noting that the agent's position is displayed as a green circle, and the velocity is displayed as a red circle. The green grid is 10 units square (although the aspect ratio of the window can stretch this scale).

It is also worth noting the AgentIMGUI method which helps output debug information to the IMGUI interface.

```cpp
static void AgentIMGui(Agent & pAgent)
{
    glm::vec2 position = pAgent.Position();
    std::string positionStr = "Pos[" + std::to_string(position.x) + "," + std::to_string(position.y) + "]";
    glm::vec2 velocity = pAgent.Velocity();
    std::string velocityStr = "Vel[" + std::to_string(velocity.x) + "," + std::to_string(velocity.y) + "]";
    ImGui::Text(positionStr.c_str());
    ImGui::Text(velocityStr.c_str());
}
```



## 2. Movement

The first thing that we want to do is update the agent's position with respect to time. To do this add an update method in the agent class.

```cpp

        void Update(float pSeconds)
        {
            m_Position = m_Position + m_Velocity * pSeconds;
        }

```
Call the method in the update loop

```cpp
        // update physics 100 times a second
        if (updateTime < 0)
        {
            agent.Update(0.01f);
            updateTime += 1.0 / 100.0f;
        }
```

### Know your next commit!

Again, you should commit your code regularly, and whenever you have implemented a specific, logical feature.

### Test and commit your code to source control

Test your code is working as expected. Initially the agent velocity is set to 10 units per second upwards. The grid is 10 units square so the agent should take one second to move one grid space. Modify the agent to test with other velocities.

If your code is working commit your code with an appropriate commit message like "Can move agent"

## 3. Wrap Around Behaviour

Next we want to implement some wrap around behaviour to stop the agent from escaping the screen. There are a number of ways to do this. To start with add a MoveTo method to the agent that will allow you to change the agent's position.

Next, in the main menthod after the agent has been updated write a test to check if the agents position is outside the grid. If it is, move the agent to the opposite side of the grid.

It would probably be easiest to deal with each of the four directions up, down, left and right individually.

### Know your next commit!

### Test and commit your code to source control

Test your code is working as expected, then commit your code with an appropriate commit message like "Implemented wrap around behaviour for agent"

## 4. Add Seek Behaviour

Next you implement seek behaviour. To do this the agent needs a target. Add a glm::vec2 datamember in the Agent class to be the target that the agent will seek - add the ability to set that target to a meaningful value.

Then, in the update behaviour use the target to create a desired velocity. To do that subject the current position from the target, then make the desired velocity vector have a length equal to the maximum speed of the agent (we want to reach the target as fast as possible).

Calculate the required Force to change the current velocity to the desired velocity by subtracting the current velocity from the desired velocity. Use that force as the acceleration. This is a bit of a hack step, and it equivalent to assuming the agent has a mass of 1 - otherwise we would divide the force by the mass of the agent to get the resultant acceleration.

Then integrate the current velocity by the acceleration by adding on the change in velocity for the appropriate timestep.

```cpp

        void Update(float pSeconds)
        {
            // get the desired velocity
            glm::vec2 desiredVelocity = glm::normalize(m_Target - m_Position);
            desiredVelocity *= 25; // set desired speed to be max speed
            glm::vec2 acceleration = desiredVelocity - m_Velocity; // calculate desired acceleration
            // note that this is not proper simulation - we are kind of assuming mass to be 1 

            // integrate velocity
            m_Velocity = m_Velocity + acceleration * pSeconds;
            // integrate position
            m_Position = m_Position + m_Velocity * pSeconds;
        }

```

### Know your next commit!

### Test and commit your code to source control

You will notice that once your agent reaches its target it will start to oscillate around it. As an extension task you could add some logic to change the target to a new one when the agent reaches it?

Test your code is working as expected, then commit your code with an appropriate commit message like "Added seek behaviour"

## 5. Add Flee Behaviour

Evade behaviour is very similar to Seek behaviour, except the desired velocity should be set to the opposite direction as shown below.

```cpp
glm::vec2 desiredVelocity = glm::normalize(m_Position - m_Target);
```
### Know your next commit!

### Test and commit your code to source control

You might see some odd behaviour from the flee behaviour where the agent gets stuck on the edge of the world. This is because of the wrap around behaviour that we wrote, and how the distance to the flee target changes drastically at the edge. To fix this (as an extension task) you could calculate the distance to multiple flee targets (the one in the world and the ones in the three closest adjacent screens.

Test your code is working as expected, then commit your code with an appropriate commit message like "Added flee behaviour"

### Summary

In this lab you've created some simple agent behaviours to govern the behaviour of the simulation. In the next lab we will expand on this by adding other individual agent behaviours and group behaviours.
