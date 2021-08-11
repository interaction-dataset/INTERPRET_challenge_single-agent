
# INTERPRET Sinlge Agent Prediction and Conditional Sinlge Agent Prediction in the ICCV21 Stage

In the Sinlge Agent Prediction and Conditional Sinlge Agent Prediction track, the target is to predict one target agents' **coordinates** in the future 3 seconds (30 frames).

Note that this instruction is for the **ICCV21** Stage of the INTERPRET challenge with the latest version of INTERACTION Dataset (**v1.2**). As for the guidance for the NeurIPS20 stage with the deprecated version of INTERACTION Dataset (v1.1), please check https://github.com/interaction-dataset/INTERPRET_challenge_regular_generalizability_track. However, since the v1.1 version is deprecated, we suggest participants to use the latest version of data and submit to the latest stage of competition.

## Submission Policy
The provided training data of the INTERPRET challenge may be used for learning the parameters of the algorithms. The test data should be used strictly for reporting the final results compared to competitors on the INTERPRET website - it must not be used in any way to train or tune the systems, for example by evaluating multiple parameters or feature choices and reporting the best results obtained. We have provided a suggested split between the training and validation sets using the INTERACTION drone dataset, and the participants can choose to use that. The tuned algorithms should then be run only once on the test data.

The evaluation server may not be used for parameter tuning. We allow participants to upload the results of their algorithm onto the server and perform all other experiments on the training and validation set.

The trajectory prediction results will be evaluated automatically and participants can decide whether each evaluation result is public in their submission log pages. The default is non-public.


## Data Format

For each csv file in the released "train" and "val" folder, the csv's name represents the scene name.  Each csv file includes multiple cases and each case includes all agents' states in 4 seconds. Note that some agents may not be fully visible in the 4 seconds. In the csv file, each row represents a agent's state at a timestamp of a case and each columns means:

case id: the id of the case under this driving scenario.

track_id: the agent id.

frame_id: the id of the current frame.

timestamp_ms: the time instant of the corresponding frame in ms.

agent_type: the type of the agent. It is either "car" or "pedestrian/bicycle".

x: the x position of the agent. Unit: m. The coordinate system of the agent is a relative coordinate system with respect to some predefined points in our recorded scenes.

y: the x position of the agent. Unit: m. The coordinate system of the agent is a relative coordinate system with respect to some predefined points in our recorded scenes.

vx: the velocity in the x-direction of the agent. Unit: m/s.

vy: the velocity in the y-direction of the agent. Unit: m/s.

psi_rad: the yaw angle of the agent if the agent is a vehicle. If the agent is a pedestrian, then this column is empty. Unit: rad.

length: the length(size) of the agent if the agent is a vehicle. If the agent is a pedestrian, then this column is empty. Unit: m.

width: the width(size) of the agent if the agent is a vehicle. If the agent is a pedestrian, then this column is empty. Unit: m.


For each csv file in the released "test_single-agent" and "test_conditional-single-agent" folder, since they are the input data of the competition, there are the following differences:

1. There are only 1 second observation of each case. The future 3 seconds is the prediction horizon.
 
2. Additional columns:  "interesting agent" indicates whether a vehicle is the ego agent of the case where 0 means no and 1 means yes. Each case only has one "interesting agent".  "track_to_predict" indicates whether a vehicle is a target agent for the trajectory prediction. All target agents are guaranteed to be fully observable in the 1+3 seconds of the raw data. Only vehicles are selected as the ego agent or the target agent.  Regarding how the columns are labeled please check the [main page of the competition](http://challenge.interaction-dataset.com/prediction-challenge/intro). Participants are free to decide the strategy of setting "interesting agent" and  "track_to_predict" in the train/val data.

## Submission for Sinlge Agent Prediction and Conditional Sinlge Agent Prediction in the ICCV21 Stage


For each scenario X like (DR_CHN_Merging_ZS0), there should be a single file 'X_sub.csv'. The following columns would be used during the evaluation: case_id, track_id, frame_id, timestamp_ms, x1, y1, x2, y2, x3, y3, x4, y4, x5, y5, x6, y6. Other columns would be ignored. The order of rows and columns could be arbitrary. 'xi, yi' represents the predicted coordinate for the vehicle 'track_id' at 'timestamp_ms' in the modality i. Up to 6 modalities would be taken into consideration. Participants could submit less than 6 modalities (like only x1, y1). Each submission could contain up to 6 modalities where the modality with higher confidence should has smaller index. In other words, modalaity 1 has the highest confidence and modality 6 has the lowest.

Csv files for all scenarios of a track should be packed into **a single zip** file for submission.

[DR_CHN_Merging_ZS0_sub.csv](https://github.com/interaction-dataset/INTERPRET_challenge_single-agent/blob/main/DR_CHN_Merging_ZS0_sub.csv) is an example for submission for the scenario DR_CHN_Merging_ZS0. Note that this example file only contains the first 3 cases with 4 modalities in each case and the input is random number.




## Metrics for Sinlge Agent Prediction and Conditional Sinlge Agent Prediction in the ICCV21 Stage

All metrics are averaged over all cases of all scenarios. The ranking of the single agent prediction is based on **MR**.

### minADE
Minimum Average Displacement Error (minADE) represents the minimum value of the euclid distance averaged by time between the ground truth and modality with the lowest value. The minADE of a single case is calculated as:

![](http://latex.codecogs.com/gif.latex?\\text{minADE}=\\min\\limits_{k\\in\\{1,...,K\\}}\\frac1{T}\\sum\\limits_{t}\\sqrt{(\\hat{x}_{t}-x_{t}^k)^2+(\\hat{y}_{t}-y_{t}^k)^2})
where T is the number of predicted timestamps which is 30 in this challenge, K is the number of modalities in this challenge, $\hat{x}$ and $\hat{y}$ means the ground truth. The final value is averaged over all cases.

### minFDE

Minimum Final Displacement Error (minFDE) represents the minimum value of the euclid distance at the last predicted timestamps between the ground truth and modality with the lowest value. The minFDE of a single case is calculated as:

![](http://latex.codecogs.com/gif.latex?\\text{minFDE}=\\min\\limits_{k\\in\\{1,...,K\\}}\\sqrt{(\\hat{x}_{T}-x_{T}^k)^2+(\\hat{y}_{T}-y_{T}^k)^2})

where T is the number of predicted timestamps which is 30 in this challenge, K is the number of modalities in this challenge, $\hat{x}$ and $\hat{y}$ means the ground truth. The final value is averaged over all cases.


### MR

For a vehicle in a modality of a case, if its prediction at the final timestamp T=30 is out of a given lateral or longitudinal threshold of the ground truth, it is considered as 'miss'. Specifically, we first rotate the groud truth and the prediction according to the ground truth yaw angle at T=30 so that the x-axis is the longitudinal direction and the y-axis is the lateral direction. The lateral threshold is set as 1 meter and the longitudinal is set as a piecewise function based on the velocity v of the ego agent's ground truth at T=30. For the high-speed scenario, the funcition gives a larger longitudinal threshold.


<img src="https://latex.codecogs.com/svg.image?\text{Threshold}_\text{lon}=\left\{\begin{matrix}1&space;&&space;v<1.4m/s&space;\\1&plus;\frac{v-1.4}{v-11}&space;&space;&&space;1.4m/s&space;\leq&space;v&space;\leq&space;11m/s\\2&space;&&space;v&space;\geq&space;11m/s&space;\\\end{matrix}\right." title="\text{Threshold}_\text{lon}=\left\{\begin{matrix}1 & v<1.4m/s \\1+\frac{v-1.4}{v-11} & 1.4m/s \leq v \leq 11m/s\\2 & v \geq 11m/s \\\end{matrix}\right." />

If the lateral or longitudinal distance between the prediction and ground truth at the last timestamp is larger than the cooresponding threshold, this vehicle in this modality of this case is considered as 'miss' - 1. If all modalities of a case are 'miss', we consider the case as 'miss' - 1. Miss Rate (miss rate) calculates the ratio of 'miss' cases over all cases.

# Note
For guidance of **INTERPRET Multi-Agent Prediction and Conditional Multi-Agent Prediction in the ICCV21 Stage**, please visit https://github.com/interaction-dataset/INTERPRET_challenge_multi-agent.


## Acknowledgement
Some metrics are inspired by the [Waymo Open Dataset - Motion Prediction Challenge](https://waymo.com/open/challenges/2021/motion-prediction/) and [Argoverse Motion Forecasting Competition](https://eval.ai/web/challenges/challenge-page/454/overview).
