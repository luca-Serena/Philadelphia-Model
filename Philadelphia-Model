globals [countsimilar
whiteSegregationCounter
]

patches-own [
  services criminality ]

turtles-own[
  age
]

;initialize the grid in a probabilistic way using real data on ethnicity, wealth, education degree and their relationships
to init
  clear-all
    ask patches [
    if (pxcor < 4) and (pycor > 4) [ set services 5.5 set pcolor 4]
    if (pxcor > 3) and (pycor > 6) [ set services 6.5 set pcolor 8 ]
    if (pxcor < -7) and (pycor < 5) [ set services 5 set pcolor 2 ]
    if (pxcor > 8) and (pycor < 7) [ set services 9 set pcolor 9.9 ]
    if (pxcor > -8) and (pxcor < 9) and (pycor > -9) and (pycor < -2) [ set services 6 set pcolor 6 ]
    if (pxcor < 9) and (pxcor > -8) and (pycor < -8) [ set services 7.5 set pcolor 9 ]
    if ((pxcor < 9) and (pxcor > -8) and (pycor < 5) and (pycor > -4)) xor ((pxcor < 9) and (pxcor > 3) and (pycor < 7) and (pycor > 4)) [ set services 3.5 set pcolor 0 ]
  ]

  while [count turtles != numturts][
     let x random 33 - 16
     let y random 33 - 16
     let busy count turtles with [xcor = x and ycor = y]
     if busy = 0 [
        create-turtles 1 [
            set xcor x
            set ycor y
            let biasAge random 100
            if (biasAge < 36) [set age random 5 + 20]
            if (biasAge >= 36 and biasAge < 64) [set age random 19 + 25]
            if (biasAge >= 64 and biasAge < 88) [set age random 19 + 45]
            if (biasAge >= 88)  [set age random 36 + 64]
            set shape "square" ; high school diploma but not degree
            set size 0.7
            let rdmcolor random 100
            if rdmcolor < 43 [set color 86]; afroamerican, blue
            if rdmcolor < 80 and rdmcolor >= 43 [set color 65];caucasian, green
            if rdmcolor < 93 and rdmcolor >= 80 [set color 15]; hispanic, red
            if rdmcolor >= 93 [set color 45]; asian, yellow
            let rdmshape random 100
            if color = 65[
               if rdmshape < 10 [set shape "x"]; no diploma
               if rdmshape >= 61 [set shape "circle"] ;graduated
            ]
            if color =  86[
               if rdmshape < 18 [set shape "x"]
               if rdmshape >= 86 [set shape "circle"]
            ]
            if color = 15 [
                if rdmshape < 36 [set shape "x"]
                if rdmshape >= 86 [set shape "circle"]
            ]
            if color = 45[
                if rdmshape < 31 [set shape "x"]
                if rdmshape >= 63 [set shape "circle"]
            ]
        let rdmsize random 100
        if shape = "x" [   ; if you don't have high school
          if rdmsize >= 86 and rdmsize < 95 [set size 0.9]
          if rdmsize > 95 [set size 1.1]   ; else 0.7 as default
        ]
        if shape = "square" [
          if rdmsize >= 40 and rdmsize < 90 [set size 0.9]
          if rdmsize > 90 [set size 1.1]
        ]
        if shape = "circle" [
          if rdmsize >= 20 and rdmsize < 70 [set size 0.9]
          if rdmsize > 70 [set size 1.1]
        ]

        ;corrector is a variable that randomize the initial earnings, since after the previous phase the only possible values are 0.7, 0.9 and 1.1
        let corrector random-float 0.4
        set corrector corrector - 0.2
        ifelse color = 65  ;the caucasian are richer at the beginning
        [set corrector corrector + random-float 0.1] ;added a number between 0 and 0.1 for white people
        [set corrector corrector - random-float 0.075] ;deducted a number between 0 and 0.075 for non-white
        set size size + corrector
        if size > 1.3 [ ask self [ set size 1.3 - random-float 0.1]]
        if size < 0.5 [ ask self [ set size 0.5 + random-float 0.1]]
        let pt one-of patches with [pxcor = x and pycor = y]
        while [[pcolor] of pt + 1 > (size * 10)] [
           set pt getDestination self false
               ask self [move-to pt]
        ]
        ]
        let cell patches with [pxcor = x and pycor = y]
        ask cell [areaSecurity self]
    ]
  ]
  reset-ticks
end


to train ; run the model after initialization
  every 1 [ask patches [areaSecurity self]]
  let turt one-of turtles
  relocate turt true
  let whiteCardinality count turtles with [color = 65]
  every 1  [checkWhiteSegregation]
  if (whiteSegregationCounter / whiteCardinality) > 0.64 [reset-ticks stop] ;stop when 64% of white segregation rate is reached
  tick
end
; Life expectancy In Philadelphia is 74 so in this case, since the average initial age is 22, the life expecancy for a turtle in the graphic is 52 (and we round it to 50). In one year has to die a person out of 60. Since a person dies averagely every 100 ticks 1year = numturts/60* 100 = numturts * 3/5 =
;The ticks per year are calculates as numturts / 50 * 100 (because 1 out of 100 ticks dies)
; We used real data excluding people younger than 20
to evolve
  let ticksPerYear  numturts * 2
  if ticks mod ticksPerYear = 0 [ask turtles [set age age + 1 ]]
  every 5 [ask patches [areaSecurity self]]
  every 5 [checkClusterLevel]
  let turt one-of turtles
  let whatToDo random 100
  if whatToDo = 0 [
  if any? turtles with [age > 100] [set turt one-of turtles with [age >= 100]]
    let biasdeath random 80
    let rdmDeath random 1259                                   ;number of total deaths (in hundreds) in Pennsylvania in 1996
    if rdmDeath < 7  and any? turtles with  [age <= 24] [set turt one-of turtles with [age <= 24]]
    if rdmDeath >= 7 and rdmDeath < 15 and any? turtles with  [age <= 29 and age >= 25 ] [set turt one-of turtles with [age <= 29 and age >= 25 ]]
    if rdmDeath >= 15 and rdmDeath < 27 and any? turtles with  [age <= 34 and age >= 30 ][set turt one-of turtles with [age <= 34 and age >= 30 ]]
    if rdmDeath >= 27 and rdmDeath < 44 and any? turtles with  [age <= 39 and age >= 35 ] [set turt one-of turtles with [age <= 39 and age >= 35 ]]
    if rdmDeath >= 44 and rdmDeath < 67 and any? turtles with  [age <= 44 and age >= 40 ] [set turt one-of turtles with [age <= 44 and age >= 40 ]]
    if rdmDeath >= 67 and rdmDeath < 97 and any? turtles with  [age <= 49 and age >= 45 ][set turt one-of turtles with [age <= 49 and age >= 45 ]]
    if rdmDeath >= 97 and rdmDeath < 132 and any? turtles with  [age <= 54 and age >= 50 ][set turt one-of turtles with [age <= 54 and age >= 50 ]]
    if rdmDeath >= 132 and rdmDeath < 180 and any? turtles with  [age <= 59 and age >= 55 ][set turt one-of turtles with [age <= 59 and age >= 55 ]]
    if rdmDeath >= 180 and rdmDeath < 248 and any? turtles with  [age <= 64 and age >= 60 ][set turt one-of turtles with [age <= 64 and age >= 60 ]]
    if rdmDeath >= 248 and rdmDeath < 360 and any? turtles with  [age <= 69 and age >= 65 ][set turt one-of turtles with [age <= 69 and age >= 65 ]]
    if rdmDeath >= 360 and rdmDeath < 523 and any? turtles with  [age <= 74 and age >= 70 ][set turt one-of turtles with [age <= 74 and age >= 70 ]]
    if rdmDeath >= 523 and rdmDeath < 719 and any? turtles with  [age <= 79 and age >= 75 ][set turt one-of turtles with [age <= 79 and age >= 75 ]]
    if rdmDeath >= 719 and rdmDeath < 926 and any? turtles with  [age <= 84 and age >= 80 ][set turt one-of turtles with [age <= 84 and age >= 85 ]]
    if rdmDeath >= 926  and any? turtles with  [age >= 85 ] [set turt one-of turtles with [age > 85 ]]
    if turt = nobody [set turt one-of turtles]
    ask turt [die]
 ]
  if whatToDo = 1 [
     create-turtles 1 [  ;a turtle is born
          while [([size] of turt - 0.4) > random-float 1 ][set turt one-of turtles] ;poor people more likely yo have sons
             set shape "x"
             set age 20 + random 5
             set color [color] of turt
             set size max list ([size] of turt - random-float 0.4) (0.5 + random-float 0.2)
          let dest getDestination self true
          while [([pcolor] of dest ) > size * 10 ][
          ifelse random 5 = 0 [set dest getDestination self false]
                              [set dest getDestination self true]
          ]
          ask self [move-to dest]
          if count turtles > 890 [ask self [die]] ; no overcrowding
     ]
  ]
   ;increase education level for a turtle who's younger than 30
  if whatToDo < 5 and whatToDo >= 2 [ if [age] of turt > 30 [set turt one-of turtles with [age < 30] ] if [shape] of turt = "x" [ask turt [set shape "square"]]]
  if whatToDo = 5 and random 4 > 1  [ if [age] of turt > 30 [set turt one-of turtles with [age < 30] ] if [shape] of turt = "square" [ask turt [set shape "circle" ]]]
  if whatToDo > 5 and whatToDo < 85 [ ;change earnings
  let sizeDiff random-float 0.1 - 0.04725 ; diploma
  if [shape] of turt = "circle"[
      set sizeDiff random-float 0.1 - 0.04575] ;more likely to increase the income ; laurea
  if [shape] of turt = "x"[
        set sizeDiff random-float 0.1 - 0.0495] ; no diploma, no laurea

  ask turt [ set size [size] of turt + sizeDiff]
  if [size] of turt > 1.3 [ ask turt [ set size 1.3 - ([size] of turt - 1.3)]]
  if [size] of turt < 0.5 [ ask turt [ set size 0.5 + (0.5 - [size] of turt) / 2]]
  ]

  if whatToDo >= 85 [ ;move away

   ;usually if you're rich you care less to be close with people of your ethnic group because you probably are already satisfied by your location
   ifelse random-float 3 < ([size] of turt - 0.5) [relocate turt false] [relocate turt true]]

  tick
end

;reset the global variable before calling checkSegregation and calculate segregation percentage
to checkClusterLevel
  set countsimilar 0
  ask patches [check one-of turtles-here]
  let cardinality count turtles
  print (word "clustering coefficient: " precision (countsimilar / cardinality) 4 " --- ricchezza media: " precision mean [size] of turtles 4 "--turtles--- " count turtles )
end


;reset the global variable before calling checkWhiteSegregation
to checkWhiteSegregation
  set whiteSegregationCounter 0
  ask patches [checkWhite one-of turtles-here]
  let cardinality count turtles with [color = 65]
end

 ;check segregation rate of turtles updating a global variable fo each turtle
to check [turt]
  let res neighborhoodsimilarity turt
  set countsimilar countsimilar + res
end

; check segregation rate of just caucasian people
to checkWhite [turt]
  if  is-turtle? turt [
    if [color] of turt = 65 [let res neighborhoodSimilarity turt set whiteSegregationCounter whiteSegregationCounter + res]]
end

;given a turtle turt move turt in another place, possibly with other turtles of its ethnic group and only if he can afford that
to relocate [turt bool]
  let result neighborhoodsimilarity turt
  let destination getDestination turt bool
  let comparison [size] of turt * 10
  let affordToStayHere satisfaction turt patch [xcor] of turt [ycor] of turt ;rich usually don't move to poor places
  if (([pcolor] of destination) + 1) < comparison  [                        ;if can afford to move in that place
      if result * 100 - affordToStayHere < random 100 [ask turt [move-to destination]]
  ]
end

; return percentage of neighbors with the same color of a turtle
to-report neighborhoodSimilarity [turt]
  let result 0
  if is-turtle? turt[
     let patchNeighs [neighbors] of turt
     let neighsNumber sum [count turtles-here] of patchNeighs
     if neighsNumber > 0 [
        ask patchNeighs [set result result + getsimilarity turt one-of turtles-here]
        set result result / neighsNumber
    ]
  ]
  report result
end

;return a free cell, first try close to a turtle of the same turt's ethcnic group, otherwise a random cell
; bool if true try to put turt close to another turtle of the same ethnic group
to-report getDestination [turt bool]
   let sim one-of turtles
   if bool = true [set sim one-of turtles with [color = [color] of turt]]
   let patchNeighs [neighbors] of sim
   let neighsNumber sum [count turtles-here] of patchNeighs
   let pt one-of patchNeighs
   let earn [size] of turt * 10
   ifelse neighsNumber < 8 and bool = true [while [[count turtles-here] of pt > 0] [set pt one-of patchNeighs]]
                           [while [[count turtles-here] of pt > 0] [set pt one-of patches with [pcolor  < earn]]]
   report pt
end

; return 1 if two turtle has same color, 0 otherwise
to-report getSimilarity [turt neigh]
   if neigh = nobody or not is-turtle? neigh or not is-turtle? turt [report 0]
   ifelse [color] of neigh = [color] of turt [report 1][report 0]
end

; old idea to calculate areasecurity through just neighbors
to areaSecurity [cell]
  let avg-wealth 1
  if any? turtles in-radius 4[
    ask cell [set avg-wealth mean [size] of turtles in-radius 4]]
  let crimeScore(avg-wealth - 0.5 ) * 12.5 ; it should return a value between 0 and 10
  ask cell [set criminality crimeScore]
  ask cell [set pcolor (crimescore + services) / 2 ]
end


 ;you're more likely to move if you can't afford to stay in a certain place or if you're rich and you live in a bad place
 ;you'll have a high return if you're poor and you live good place of viceversa
 ;you compare pcolor of cells +2 and size of turtles * 10 to determine the affordance
to-report satisfaction [turt cell]
  let cellColor [pcolor] of cell
  let turtSize [size] of turt * 10
  if [size] of turt > 1.1 and [pcolor] of cell > 7.5 [report -50] ; if you're rich and in a good place you're not likely to move
  if turtSize - 1 < cellColor [ report 100]
  let affordance abs (([pcolor] of cell + 2 ) - ([size] of turt * 10))
  report affordance * 20
end
