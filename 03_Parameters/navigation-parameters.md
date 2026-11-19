---
type: parameter
title: "Navigation parameter catalog and configuration selection"
source_repo: seljb/pinky_pro
source_commit: "014a09f289e6988894fbffde2624fdb1e257815b"
source_paths:
  - pinky_navigation/params/nav2_params.yaml
  - pinky_navigation/params/mapper_params.yaml
  - pinky_navigation/launch/bringup_launch.xml
  - pinky_navigation/launch/navigation_launch.xml
  - pinky_navigation/launch/localization_launch.xml
  - pinky_navigation/scripts/nav2_web_server.py
  - pinky_navigation/map/my_map.yaml
  - pinky_navigation/map/pinklab.yaml
verification: 코드 확인
---

# Navigation parameter catalog and configuration selection

## 1. Basic information

- Package: `pinky_navigation`.
- Primary files: tracked `params/nav2_params.yaml` and `params/mapper_params.yaml`.
- Related nodes: AMCL, BT navigator, controller, local/global costmaps, planner, path smoother, behavior server, waypoint follower, velocity smoother and SLAM Toolbox.
- Source values: recorded below at the metadata SHA.
- Actual runtime values: **unknown**; no nodes were queried or run.
- Units below are taken from explicit source comments where available, or are identified as interpretation of configured geometry/time names. They are not measurements.

### Which file is actually selected?

| Entry | Source default / forwarding | Evidence |
|---|---|---|
| Upper `bringup_launch.xml` and `gz_bringup_launch.xml` | Local `pinky_navigation/params/nav2_params.yaml`, forwarded to both localization and navigation includes | [pinky_navigation/launch/bringup_launch.xml:8–8](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/launch/bringup_launch.xml#L8-L8); [pinky_navigation/launch/bringup_launch.xml:34–52](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/launch/bringup_launch.xml#L34-L52); [pinky_navigation/launch/gz_bringup_launch.xml:8–8](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/launch/gz_bringup_launch.xml#L8-L8) |
| Standalone `navigation_launch.xml` | External `nav2_bringup/params/nav2_params.yaml` | [pinky_navigation/launch/navigation_launch.xml:5–9](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/launch/navigation_launch.xml#L5-L9) |
| `web_slam` and `gz_web_slam` | Explicitly pass local Nav2 and mapper YAMLs | [pinky_navigation/launch/web_slam.launch.xml:6–22](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/launch/web_slam.launch.xml#L6-L22); [pinky_navigation/launch/gz_web_slam.launch.xml:6–22](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/launch/gz_web_slam.launch.xml#L6-L22) |
| Mapping wrappers | Local `mapper_params.yaml`, forwarded to external synchronous SLAM launch | [pinky_navigation/launch/map_building.launch.xml:3–8](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/launch/map_building.launch.xml#L3-L8); [pinky_navigation/launch/gz_map_building.launch.xml:3–8](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/launch/gz_map_building.launch.xml#L3-L8) |

A value in this catalog describes the selected source file, not all possible launches. Launch-level `use_sim_time` parameters are supplied after YAML in the server blocks. Their final live application still needs inspection. **[Code verified / runtime unverified]** [pinky_navigation/launch/navigation_launch.xml:21–25](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/launch/navigation_launch.xml#L21-L25); [pinky_navigation/launch/navigation_launch.xml:98–103](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/launch/navigation_launch.xml#L98-L103)

## 2. Parameter roles

### Localization, map and frames

| Node / key | Configured value | Why it matters | Evidence |
|---|---|---|---|
| AMCL `base_frame_id`, `global_frame_id`, `odom_frame_id` | `base_footprint`, `map`, `odom` | Frame names expected by localization | [pinky_navigation/params/nav2_params.yaml:8–22](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/params/nav2_params.yaml#L8-L22) |
| AMCL `scan_topic`, `tf_broadcast` | `scan`, `true` | Sensor name and configured transform publication | [pinky_navigation/params/nav2_params.yaml:31–41](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/params/nav2_params.yaml#L31-L41) |
| AMCL particle limits | `min_particles=500`, `max_particles=2000` | Configured estimator population bounds; external algorithm not analyzed | [pinky_navigation/params/nav2_params.yaml:18–24](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/params/nav2_params.yaml#L18-L24) |
| AMCL update thresholds | `update_min_a=0.1`, `update_min_d=0.15` | Source angular/distance update thresholds | [pinky_navigation/params/nav2_params.yaml:31–34](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/params/nav2_params.yaml#L31-L34) |
| AMCL initial-pose keys | `set_initial_pose: true`, `initial_pose: [0, 0, 0]` | Exact source representation; compatibility with installed Nav2 is unverified | [pinky_navigation/params/nav2_params.yaml:39–41](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/params/nav2_params.yaml#L39-L41) |
| BT navigator | `global_frame=map`, `robot_base_frame=base_link`, `odom_topic=odom` | Navigator state references; note `base_link` vs AMCL's `base_footprint` | [pinky_navigation/params/nav2_params.yaml:43–47](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/params/nav2_params.yaml#L43-L47) |
| Map server filename | Launch overrides `yaml_filename` with `map` argument | Source YAML has only commented map_server example | [pinky_navigation/launch/localization_launch.xml:18–21](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/launch/localization_launch.xml#L18-L21); [pinky_navigation/params/nav2_params.yaml:252–257](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/params/nav2_params.yaml#L252-L257) |

| Map metadata | `my_map.yaml` | `pinklab.yaml` |
|---|---|---|
| Image | `my_map.pgm` | `pinklab.png` |
| Resolution | `0.05` | `0.05` |
| Origin | `[-2.05, -1.62, 0]` | `[-0.65, -10.5, 0]` |
| Mode / negate | `trinary` / `0` | `trinary` / `0` |
| Occupied / free threshold | `0.65` / `0.25` | `0.65` / `0.25` |

**[Code verified]** [pinky_navigation/map/my_map.yaml:1–7](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/map/my_map.yaml#L1-L7); [pinky_navigation/map/pinklab.yaml:1–7](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/map/pinklab.yaml#L1-L7). These metadata do not demonstrate that either image matches the current room or world.

### Controller and goal/progress checks

| Group / key | Source value | Learning interpretation | Evidence |
|---|---|---|---|
| `FollowPath.plugin` | `nav2_regulated_pure_pursuit_controller::RegulatedPurePursuitController` | Chooses the external route-following implementation | [pinky_navigation/params/nav2_params.yaml:78–80](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/params/nav2_params.yaml#L78-L80) |
| `desired_linear_vel` | `0.2` m/s in source comment | Requested nominal forward speed, not measured speed | [pinky_navigation/params/nav2_params.yaml:82–85](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/params/nav2_params.yaml#L82-L85) |
| `allow_reversing`, `use_rotate_to_heading` | `false`, `true` | Configured pursuit/heading options; behavior server backup is separate | [pinky_navigation/params/nav2_params.yaml:83–99](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/params/nav2_params.yaml#L83-L99); [pinky_navigation/params/nav2_params.yaml:293–298](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/params/nav2_params.yaml#L293-L298) |
| Lookahead distance/time | Base `0.6`, scaled `true`, time `1.5`, bounds `0.3..0.9` | Parameters governing where the controller looks along the path | [pinky_navigation/params/nav2_params.yaml:87–94](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/params/nav2_params.yaml#L87-L94) |
| Heading rotation | Threshold `0.35` rad, speed `1.0` rad/s | Source rotation threshold/speed | [pinky_navigation/params/nav2_params.yaml:96–99](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/params/nav2_params.yaml#L96-L99) |
| Curvature / cost scaling | Curvature scaling `false`; cost scaling `true`, distance `0.6`, gain `1.5` | Distinct regulation mechanisms; a disabled mechanism's numeric settings still exist | [pinky_navigation/params/nav2_params.yaml:101–110](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/params/nav2_params.yaml#L101-L110) |
| Goal approach | Distance `0.6`, minimum velocity `0.05` | Source approach slowing options | [pinky_navigation/params/nav2_params.yaml:112–114](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/params/nav2_params.yaml#L112-L114) |
| Collision projection | Enabled `true`, time `1.0` | Configured prospective collision checking; not a safety guarantee | [pinky_navigation/params/nav2_params.yaml:116–120](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/params/nav2_params.yaml#L116-L120) |
| Cancel deceleration | Enabled `false`, deceleration `3.2` | Numeric value alone does not enable the option | [pinky_navigation/params/nav2_params.yaml:122–125](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/params/nav2_params.yaml#L122-L125) |
| Server frequency / message form | `20.0`, `enable_stamped_cmd_vel=false` | Source requests 20 Hz control and unstamped commands | [pinky_navigation/params/nav2_params.yaml:130–142](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/params/nav2_params.yaml#L130-L142) |
| Goal checker | `SimpleGoalChecker`, xy `0.25`, yaw `0.25`, stateful `true` | Configured goal acceptance tolerances | [pinky_navigation/params/nav2_params.yaml:153–159](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/params/nav2_params.yaml#L153-L159) |
| Progress checker | `SimpleProgressChecker`, time `10.0`, radius `0.5` | Configured motion-progress thresholds | [pinky_navigation/params/nav2_params.yaml:165–170](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/params/nav2_params.yaml#L165-L170) |

The comments explain intended units and effects; they are not a substitute for inspecting the installed controller's implementation or live parameters. **[Code verified values / external behavior unverified]**

### Costmaps and planner

| Setting | Local costmap | Global costmap | Evidence |
|---|---|---|---|
| Frame / base | `odom` / `base_footprint` | `map` / `base_footprint` | [pinky_navigation/params/nav2_params.yaml:176–180](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/params/nav2_params.yaml#L176-L180); [pinky_navigation/params/nav2_params.yaml:220–223](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/params/nav2_params.yaml#L220-L223) |
| Update / publish frequency | `5.0` / `2.0` | `1.0` / `1.0` | [pinky_navigation/params/nav2_params.yaml:176–177](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/params/nav2_params.yaml#L176-L177); [pinky_navigation/params/nav2_params.yaml:220–221](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/params/nav2_params.yaml#L220-L221) |
| Resolution | `0.05` | `0.05` | [pinky_navigation/params/nav2_params.yaml:181–185](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/params/nav2_params.yaml#L181-L185); [pinky_navigation/params/nav2_params.yaml:225–226](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/params/nav2_params.yaml#L225-L226) |
| Footprint | Square vertices at ±`0.06` | Same square; `footprint_padding=0.03` | [pinky_navigation/params/nav2_params.yaml:185–185](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/params/nav2_params.yaml#L185-L185); [pinky_navigation/params/nav2_params.yaml:219–225](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/params/nav2_params.yaml#L219-L225) |
| Window | Rolling, width `3`, height `3` | Unknown space tracked | [pinky_navigation/params/nav2_params.yaml:180–183](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/params/nav2_params.yaml#L180-L183); [pinky_navigation/params/nav2_params.yaml:227–228](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/params/nav2_params.yaml#L227-L228) |
| Enabled plugin list | `voxel_layer`, `inflation_layer` | `static_layer`, `obstacle_layer`, `inflation_layer` | [pinky_navigation/params/nav2_params.yaml:186–192](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/params/nav2_params.yaml#L186-L192); [pinky_navigation/params/nav2_params.yaml:228–230](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/params/nav2_params.yaml#L228-L230) |
| Scan source / ranges | `/scan`, obstacle max `2.5`, raytrace max `3.0` | Same source and maxima | [pinky_navigation/params/nav2_params.yaml:200–210](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/params/nav2_params.yaml#L200-L210); [pinky_navigation/params/nav2_params.yaml:232–242](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/params/nav2_params.yaml#L232-L242) |
| Inflation | Radius `0.15`, cost factor `3.0` | Radius `0.15`, cost factor `3.0` | [pinky_navigation/params/nav2_params.yaml:187–190](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/params/nav2_params.yaml#L187-L190); [pinky_navigation/params/nav2_params.yaml:246–249](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/params/nav2_params.yaml#L246-L249) |

`local_costmap.static_layer` has a configuration block but is absent from the local `plugins` list. A block's presence alone does not establish that the layer is loaded. **[Code verified / intended loading inferred]** [pinky_navigation/params/nav2_params.yaml:186–214](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/params/nav2_params.yaml#L186-L214)

The planner selects `NavfnPlanner` under `GridBased`, with tolerance `0.5`, `use_astar=false`, `allow_unknown=true`. The separate path smoother selects `SimpleSmoother`, tolerance `1e-10`, maximum iterations `1000` and refinement true. **[Code verified]** [pinky_navigation/params/nav2_params.yaml:266–284](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/params/nav2_params.yaml#L266-L284)

### Velocity smoother and SLAM

| Node / keys | Source values | Interpretation | Evidence |
|---|---|---|---|
| Velocity smoother frequency / feedback | `20.0`, `OPEN_LOOP` | Source filter update and feedback mode | [pinky_navigation/params/nav2_params.yaml:325–329](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/params/nav2_params.yaml#L325-L329) |
| Velocity bounds | Max `[0.25, 0.0, 1.5]`, min `[-0.25, 0.0, -1.5]` | Configured vector limits; live effect unverified | [pinky_navigation/params/nav2_params.yaml:330–331](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/params/nav2_params.yaml#L330-L331) |
| Acceleration/deceleration | `[2.5,0,3.2]` / `[-2.5,0,-3.2]` | Source change-rate bounds | [pinky_navigation/params/nav2_params.yaml:332–333](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/params/nav2_params.yaml#L332-L333) |
| Timeout / message form | `1.0`, stamped command `false` | Separate from web request completion or base-driver timeout | [pinky_navigation/params/nav2_params.yaml:334–338](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/params/nav2_params.yaml#L334-L338) |
| SLAM frame / mode | `odom`, `map`, `base_footprint`, `/scan`, `mapping` | Mapping input references | [pinky_navigation/params/mapper_params.yaml:13–17](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/params/mapper_params.yaml#L13-L17) |
| SLAM timing / resolution | Transform period `0.02`, map interval `5.0`, resolution `0.05`, minimum interval `0.5` | Separate map/transform/update settings | [pinky_navigation/params/mapper_params.yaml:19–27](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/params/mapper_params.yaml#L19-L27) |
| SLAM motion / loop closure | Travel distance `0.5`, heading `0.5`, loop closing `true` | Configured scan processing and loop options | [pinky_navigation/params/mapper_params.yaml:31–45](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/params/mapper_params.yaml#L31-L45) |
| SLAM solver | `solver_plugins::CeresSolver` | External solver selected; its implementation not inspected | [pinky_navigation/params/mapper_params.yaml:4–10](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/params/mapper_params.yaml#L4-L10) |
| Web `ip`, `port` | Code `192.168.4.1:8080`; wrappers `0.0.0.0:8080` | Launch defaults differ from the class default | [pinky_navigation/scripts/nav2_web_server.py:63–68](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/scripts/nav2_web_server.py#L63-L68); [pinky_navigation/launch/web_nav2.launch.xml:3–11](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/launch/web_nav2.launch.xml#L3-L11) |

## 3. Related code

The package does not read controller/costmap settings in its own Python functions. It passes YAML to external Nav2 nodes/components. `Nav2WebBridge.__init__` itself declares `ip` and `port`; its goal/pose frame is hard-coded `map`, and its pose lookup is hard-coded `map` / `base_link`. **[Code verified]** [pinky_navigation/launch/navigation_launch.xml:21–93](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/launch/navigation_launch.xml#L21-L93); [pinky_navigation/scripts/nav2_web_server.py:63–68](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/scripts/nav2_web_server.py#L63-L68); [pinky_navigation/scripts/nav2_web_server.py:217–237](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/scripts/nav2_web_server.py#L217-L237); [pinky_navigation/scripts/nav2_web_server.py:325–365](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/scripts/nav2_web_server.py#L325-L365)

## 4. Relationships between parameters

- **[Inference]** Nominal controller speed (`0.2`) and smoother maximum (`0.25` forward component) are different stages; changing only one does not remove the other stage's limit. The motor conversion also applies its own RPM scaling. Evidence: controller/smoother tables and [base parameters](pinky_bringup-parameters.md).
- **[Inference]** Footprint, padding, inflation and collision checking jointly shape navigable clearance. The YAML square spans `0.12` by `0.12` before padding, derived from its vertices. These values need comparison with actual geometry before any tuning. Evidence: costmap table.
- **[Code verified]** The controller's `inflation_cost_scaling_factor=3.0` numerically matches both inflation-layer cost factors, but controller `cost_scaling_dist=0.6` exceeds configured layer radius `0.15`. This is a relationship to review with the installed plugin, not proof of a defect. [pinky_navigation/params/nav2_params.yaml:106–110](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/params/nav2_params.yaml#L106-L110); [pinky_navigation/params/nav2_params.yaml:187–190](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/params/nav2_params.yaml#L187-L190); [pinky_navigation/params/nav2_params.yaml:246–249](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/params/nav2_params.yaml#L246-L249)
- **[Code verified]** Different nodes use `base_link` and `base_footprint`; a complete TF connection is required by the intended architecture. See [navigation flow](../01_Architecture/navigation-pipeline.md).

## 5. Expected effects of changes

These are study hypotheses, not recommendations to change the robot now.

| Question | First relevant keys | What still needs measurement / verification |
|---|---|---|
| “Why is forward travel slower than the source nominal speed?” | `desired_linear_vel`, cost/approach regulation, smoother bounds, base RPM scaling | Live overrides, cost values, command stream and actual odometry |
| “Why does a narrow route get rejected?” | Footprint, padding, inflation, map resolution, planner tolerance | Real geometry, map alignment and actual collision checks |
| “Why is arrival accepted away from the clicked point?” | Goal-checker xy/yaw tolerances; planner tolerance is a different setting | Accepted goal, active checker and final pose |
| “Why is the map slow to refresh?” | SLAM map interval vs costmap update/publish vs browser 500 ms polling | Which layer is stale, TF timestamps and network/display behavior |
| “Why are simulated poses inconsistent?” | `use_sim_time`, `/clock`, frame chain, web wrapper forwarding | Live parameter values and timestamps; `gz_web_nav2` omits web clock override |

The last row is supported by the launch difference: [pinky_navigation/launch/gz_web_nav2.launch.xml:5–17](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/launch/gz_web_nav2.launch.xml#L5-L17); [pinky_navigation/launch/gz_web_slam.launch.xml:5–17](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/launch/gz_web_slam.launch.xml#L5-L17). Browser polling: [pinky_navigation/scripts/index.html:1060–1062](https://github.com/seljb/pinky_pro/blob/014a09f289e6988894fbffde2624fdb1e257815b/pinky_navigation/scripts/index.html#L1060-L1062).

## 6. Related documents

- [Navigation package](../02_Packages/pinky_navigation.md)
- [Navigation and mapping pipeline](../01_Architecture/navigation-pipeline.md)
- [Web bridge analysis](../05_Code_Analysis/navigation-web-control.md)
- [Base geometry and limits](pinky_bringup-parameters.md)

## 7. Verification and cautions

**[Code verified]** Exact values and launch selection shown here. **[Inference]** Parameter relationships and proposed investigation order. **[Unverified]** Every final applied value, external plugin semantics/version support, tuning benefit and physical behavior. No YAML was edited and no robot/simulator was started.

### Check yourself

1. Is the local `static_layer` block enough to prove the layer is active? **Answer:** No; inspect the `plugins` list as well.
2. Does changing the source YAML prove a running node uses the change? **Answer:** No; identify the launch-selected file, installation state and live override values.
3. Are path smoothing and velocity smoothing settings interchangeable? **Answer:** No; they configure separate servers at separate stages.

> 작업 메모: 원본 YAML 주석의 설명을 실제 외부 플러그인 검증 결과로 승격하지 않았다. 현재 값과 실행 적용값을 분리해 기록했다.
