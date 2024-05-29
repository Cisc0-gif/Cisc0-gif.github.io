---
layout: page
title: Procedural Animation Demo
description: Procedural animation written in C# using IK
img: assets/img/project_07/procedural_animation_demo_screenshot.png
importance: 5
category: prototypes
---

A small scene created in Unity (URP 2022) and Blender.

<iframe width="100%" height="350" src="https://www.youtube.com/embed/-YecVbyKXdQ?si=VngZJs3ZF4g-n0bI" title="YouTube video player" frameborder="0" align="center" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>
<br>
<br>

<div class="row mt-3" align="center">
    <div class="col-sm mt-3 mt-md-0">
        {% include video.liquid path="https://www.youtube.com/embed/lvs-_xxHEWM?si=S7QMp_ao63TS8afn" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include video.liquid path="https://www.youtube.com/embed/sJSA5Zyg7WM?si=Hvq-pSDkkbUIuPh6" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    These videos from <a href="https://www.youtube.com/@giists5197">giists</a> proved to be a very helpful source for programming the basic movement.
</div>

IKMovement.cs
```c#
#if UNITY_EDITOR
using UnityEditor;
#endif
using UnityEngine;

public class IKMovement : MonoBehaviour
{
    //Chain length of bones
    public int ChainLength = 2;

    //Target the chain should bent to
    public Transform Target;
    public Transform Pole;

    //Solver iterations per update
    [Header("Solver Parameters")]
    public int Iterations = 10;

    //Distance when the solver stops
    public float Delta = 0.001f;

    //Strength of going back to the start position.
    [Range(0, 1)]
    public float SnapBackStrength = 1f;

    protected float[] BonesLength; //Target to Origin
    protected float CompleteLength;
    protected Transform[] Bones;
    protected Vector3[] Positions;
    protected Vector3[] StartDirectionSucc;
    protected Quaternion[] StartRotationBone;
    protected Quaternion StartRotationTarget;
    protected Transform Root;


    // Start is called before the first frame update
    void Awake()
    {
        Init();
    }

    void Init()
    {
        //initial array
        Bones = new Transform[ChainLength + 1];
        Positions = new Vector3[ChainLength + 1];
        BonesLength = new float[ChainLength];
        StartDirectionSucc = new Vector3[ChainLength + 1];
        StartRotationBone = new Quaternion[ChainLength + 1];

        //find root
        Root = transform;
        for (var i = 0; i <= ChainLength; i++)
        {
            if (Root == null)
                throw new UnityException("The chain value is longer than the ancestor chain!");
            Root = Root.parent;
        }

        //init target
        if (Target == null)
        {
            Target = new GameObject(gameObject.name + " Target").transform;
            SetPositionRootSpace(Target, GetPositionRootSpace(transform));
        }
        StartRotationTarget = GetRotationRootSpace(Target);


        //init data
        var current = transform;
        CompleteLength = 0;
        for (var i = Bones.Length - 1; i >= 0; i--)
        {
            Bones[i] = current;
            StartRotationBone[i] = GetRotationRootSpace(current);

            if (i == Bones.Length - 1)
            {
                //leaf
                StartDirectionSucc[i] = GetPositionRootSpace(Target) - GetPositionRootSpace(current);
            }
            else
            {
                //mid bone
                StartDirectionSucc[i] = GetPositionRootSpace(Bones[i + 1]) - GetPositionRootSpace(current);
                BonesLength[i] = StartDirectionSucc[i].magnitude;
                CompleteLength += BonesLength[i];
            }

            current = current.parent;
        }



    }

    // Update is called once per frame
    void LateUpdate()
    {
        ResolveIK();
    }

    private void ResolveIK()
    {
        if (Target == null)
            return;

        if (BonesLength.Length != ChainLength)
            Init();

        //Fabric

        //  root
        //  (bone0) (bonelen 0) (bone1) (bonelen 1) (bone2)...
        //   x--------------------x--------------------x---...

        //get position
        for (int i = 0; i < Bones.Length; i++)
            Positions[i] = GetPositionRootSpace(Bones[i]);

        var targetPosition = GetPositionRootSpace(Target);
        var targetRotation = GetRotationRootSpace(Target);

        //1st is possible to reach?
        if ((targetPosition - GetPositionRootSpace(Bones[0])).sqrMagnitude >= CompleteLength * CompleteLength)
        {
            //just strech it
            var direction = (targetPosition - Positions[0]).normalized;
            //set everything after root
            for (int i = 1; i < Positions.Length; i++)
                Positions[i] = Positions[i - 1] + direction * BonesLength[i - 1];
        }
        else
        {
            for (int i = 0; i < Positions.Length - 1; i++)
                Positions[i + 1] = Vector3.Lerp(Positions[i + 1], Positions[i] + StartDirectionSucc[i], SnapBackStrength);

            for (int iteration = 0; iteration < Iterations; iteration++)
            {
                //https://www.youtube.com/watch?v=UNoX65PRehA
                //back
                for (int i = Positions.Length - 1; i > 0; i--)
                {
                    if (i == Positions.Length - 1)
                        Positions[i] = targetPosition; //set it to target
                    else
                        Positions[i] = Positions[i + 1] + (Positions[i] - Positions[i + 1]).normalized * BonesLength[i]; //set in line on distance
                }

                //forward
                for (int i = 1; i < Positions.Length; i++)
                    Positions[i] = Positions[i - 1] + (Positions[i] - Positions[i - 1]).normalized * BonesLength[i - 1];

                //close enough?
                if ((Positions[Positions.Length - 1] - targetPosition).sqrMagnitude < Delta * Delta)
                    break;
            }
        }

        //move towards pole
        if (Pole != null)
        {
            var polePosition = GetPositionRootSpace(Pole);
            for (int i = 1; i < Positions.Length - 1; i++)
            {
                var plane = new Plane(Positions[i + 1] - Positions[i - 1], Positions[i - 1]);
                var projectedPole = plane.ClosestPointOnPlane(polePosition);
                var projectedBone = plane.ClosestPointOnPlane(Positions[i]);
                var angle = Vector3.SignedAngle(projectedBone - Positions[i - 1], projectedPole - Positions[i - 1], plane.normal);
                Positions[i] = Quaternion.AngleAxis(angle, plane.normal) * (Positions[i] - Positions[i - 1]) + Positions[i - 1];
            }
        }

        //set position & rotation
        for (int i = 0; i < Positions.Length; i++)
        {
            if (i == Positions.Length - 1)
                SetRotationRootSpace(Bones[i], Quaternion.Inverse(targetRotation) * StartRotationTarget * Quaternion.Inverse(StartRotationBone[i]));
            else
                SetRotationRootSpace(Bones[i], Quaternion.FromToRotation(StartDirectionSucc[i], Positions[i + 1] - Positions[i]) * Quaternion.Inverse(StartRotationBone[i]));
            SetPositionRootSpace(Bones[i], Positions[i]);
        }
    }

    private Vector3 GetPositionRootSpace(Transform current)
    {
        if (Root == null)
            return current.position;
        else
            return Quaternion.Inverse(Root.rotation) * (current.position - Root.position);
    }

    private void SetPositionRootSpace(Transform current, Vector3 position)
    {
        if (Root == null)
            current.position = position;
        else
            current.position = Root.rotation * position + Root.position;
    }

    private Quaternion GetRotationRootSpace(Transform current)
    {
        //inverse(after) * before => rot: before -> after
        if (Root == null)
            return current.rotation;
        else
            return Quaternion.Inverse(current.rotation) * Root.rotation;
    }

    private void SetRotationRootSpace(Transform current, Quaternion rotation)
    {
        if (Root == null)
            current.rotation = rotation;
        else
            current.rotation = Root.rotation * rotation;
    }

    void OnDrawGizmos()
    {
        #if UNITY_EDITOR
                var current = this.transform;
                for (int i = 0; i < ChainLength && current != null && current.parent != null; i++)
                {
                    var scale = Vector3.Distance(current.position, current.parent.position) * 0.1f;
                    Handles.matrix = Matrix4x4.TRS(current.position, Quaternion.FromToRotation(Vector3.up, current.parent.position - current.position), new Vector3(scale, Vector3.Distance(current.parent.position, current.position), scale));
                    Handles.color = Color.green;
                    Handles.DrawWireCube(Vector3.up * 0.5f, Vector3.one);
                    current = current.parent;
                }
        #endif
    }

}
```
<br>

LegMovement.cs
```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class LegMovement : MonoBehaviour
{

    //raycast point above legs that shoots ray at ground/wall

    [SerializeField]
    private Transform raycastPoint;

    [SerializeField]
    private Transform target;

    [SerializeField]
    private Transform stepPoint; //used if wall colliding with raycast

    [SerializeField]
    private GameObject player;

    [SerializeField]
    private LayerMask mask;

    [SerializeField]
    private float offset;

    [SerializeField]
    private float moveDist;

    [SerializeField]
    private float speed = 10f;

    [SerializeField]
    private LegMovement otherLeg;

    [SerializeField]
    private int moveVal;

    private Vector3 restPos;
    public Vector3 newPos;
    public  Vector3 stepNormal;

    public bool Grounded;

    private static int currentMoveVal = 1; //1 = FL & BR, 2 = FR & BL

    private bool hasMoved; //check if leg has moved
    private bool moving;
    private bool movingDown;

    private float moveDir
	{
        get
		{
            return SpiderController.instance.move_y;
		}
	}

    private bool moveBackwards = false;

    public GameObject movingObj;


    // Start is called before the first frame update
    void Start()
    {
        restPos = target.position;
        //stepPoint.position = new Vector3(restPos.x + offset, restPos.y, restPos.z);
    }

    // Update is called once per frame
    void Update()
    {
        /*
        if (moveDir > 0 && !moveBackwards)
		{
            moveBackwards = true;
            offset = -0.6f;
            stepPoint.localPosition = new Vector3(stepPoint.localPosition.x + offset, stepPoint.localPosition.y, stepPoint.localPosition.z);
		}
        else if (moveDir < 0 && moveBackwards)
		{
            moveBackwards = false;
            offset = 0.6f;
            stepPoint.localPosition = new Vector3(stepPoint.localPosition.x + offset, stepPoint.localPosition.y, stepPoint.localPosition.z);
        }
        */

        newPos = calcPoint(stepPoint.position);
        if (Vector3.Distance(restPos, newPos) > moveDist || moving && Grounded) //if distance between current leg pos is too far OR leg is ready to move and is on ground, move leg
        {
            Step(newPos);
        }
        updateIK();
    }

    public Vector3 calcPoint(Vector3 pos) //calc where leg is going to move to
	{
        Vector3 dir = pos - raycastPoint.position;
        RaycastHit hit;

        if (Physics.SphereCast(raycastPoint.position, .5f, dir, out hit, 5f, mask)) //Physics.SphereCast is like raycast but with sphere (anything that collides w/ Sphere) SphereCast(Vector3 origin, float radius, Vector3 dir, out RaycastHit hit, float maxDistance, LayerMask mask);
        {
            stepNormal = hit.normal;
            pos = hit.point; //point raycast hits (floor or wall)
            Grounded = true;

            if (hit.transform.gameObject.CompareTag("Moving"))
			{
                movingObj = hit.transform.gameObject;
			}
            else
			{
                movingObj = null;
			}
        } 
        else
		{
            stepNormal = Vector3.zero;
            pos = restPos;
            Grounded = false;
		}

        return pos;
	}

    public void Step(Vector3 pos)
	{
        if (currentMoveVal == moveVal)
        {
            Grounded = false;
            hasMoved = false;
            moving = true;

            target.position = Vector3.MoveTowards(target.position, pos + Vector3.up, speed * Time.deltaTime);
            restPos = Vector3.MoveTowards(target.position, pos + Vector3.up, speed * Time.deltaTime); //MIGHT BE BUG, SHOULD JUST SET TO target.position AFTER

            if (target.position == pos + Vector3.up) //if leg has reached point to start moving down
            {
                movingDown = true;
            }

            if (movingDown)
            {
                target.position = Vector3.MoveTowards(target.position, pos, speed * Time.deltaTime);
                restPos = Vector3.MoveTowards(target.position, pos, speed * Time.deltaTime);
            }

            if (target.position == pos)
            {
                Grounded = true;
                hasMoved = true;
                moving = false;
                movingDown = false;

                if (currentMoveVal == moveVal && otherLeg.hasMoved == true)
                {
                    currentMoveVal = currentMoveVal * -1 + 3; //if leg == 1, set val to 2
                }

            }

        }

    }

    public void updateIK()
	{
        target.position = restPos;
	}

}
```
<br>

SpiderController.cs
```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class SpiderController : MonoBehaviour
{
    private Rigidbody rb;

    [SerializeField]
    private float speed;

    [SerializeField]
    private float runSpeed;

    [SerializeField]
    private float angSpeed;

    [SerializeField]
    private float run_angSpeed;

    [SerializeField]
    private int[] refLeg;

    [SerializeField]
    private int[] refLegOpp;

    [SerializeField]
    private LegMovement[] legs;

    [SerializeField]
    private AnimationCurve sensCurve;

    [SerializeField]
    private float targetSurfDist = -1f;

    private bool Grounded;
    private float initSpeed;
    private float initangSpeed;

    private float move_x;

    [HideInInspector]
    public float move_y;

    // Start is called before the first frame update
    void Start()
    {
        rb = gameObject.GetComponent<Rigidbody>();
        initSpeed = speed;
        initangSpeed = angSpeed;

        Cursor.lockState = CursorLockMode.Locked;
        Cursor.visible = false;
        Application.targetFrameRate = 60;
        QualitySettings.vSyncCount = 1;
    }

    //Make accessible from other Scripts
    public static SpiderController instance;
    private void Awake()
	{
        instance = this;
	}

    // Update is called once per frame
    void Update()
    {
        move_x = Input.GetAxis("Horizontal");
        move_y = Input.GetAxis("Vertical");

        if (move_y != 0)
		{
            Vector3 movement = transform.right * move_y * speed * Time.deltaTime;
            rb.velocity = movement;
        }

        transform.Rotate(0, move_x * angSpeed * Time.deltaTime, 0); //rotate 

        calcOrient();

        if (Input.GetButtonDown("Fire3"))
		{
            speed = runSpeed;
            angSpeed = run_angSpeed;
		}

        if (Input.GetButtonUp("Fire3"))
		{
            speed = initSpeed;
            angSpeed = initangSpeed;
		}
    }

    public void calcOrient()
	{
        Vector3 up = Vector3.zero;
        Vector3 point, a, b, c;
        float avgSurfDist = 0;
        Grounded = false;

        for (var i = 0; i < legs.Length; i++) //get pos leg wants to step on
		{
            Debug.Log("Draw ray");
            point = legs[i].newPos;
            a = ((legs[refLeg[i]].newPos) - point).normalized;
            b = ((legs[refLegOpp[i]].newPos) - point).normalized;
            avgSurfDist += transform.InverseTransformPoint(point).y;
            c = Vector3.Cross(a, b); //get distance between legs vertically and horizontally    =||=

            Debug.DrawRay(point, a, Color.red, 0);
            Debug.DrawRay(point, b, Color.red, 0);
            Debug.DrawRay(point, c, Color.red, 0);

            up += c * sensCurve.Evaluate(c.magnitude) + (legs[i].stepNormal == Vector3.zero ? transform.forward : legs[i].stepNormal); //if legs[i].stepNormal == Vector3.zero; return gameObject.transform.forward, else, return legs[i].stepNormal
            Grounded |= legs[i].Grounded; //if legs[i].Grounded == true, Grounded = true, else null

		}

        up /= legs.Length;
        avgSurfDist /= legs.Length;

        transform.rotation = Quaternion.Slerp(transform.rotation, Quaternion.LookRotation(Vector3.ProjectOnPlane(transform.forward, up), up), 20f * Time.deltaTime);

        if (Grounded)
		{
            transform.Translate(0, -(-avgSurfDist + targetSurfDist), 0, Space.Self);
		}
        /*
        else
		{
            gameObject.transform.Translate(0, -10 * Time.deltaTime, 0, Space.Self);
        }
        */
        
	}

    public void maintainVelocity()
	{
        transform.parent = null;

        for (int i = 0; i < legs.Length; i++)
		{
            if (legs[i].movingObj == null)
			{
                continue;
			}
            else
			{
                transform.parent = legs[i].movingObj.transform;
			}
		}

	}

}
```