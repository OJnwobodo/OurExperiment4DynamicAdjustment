using UnityEngine;
using System.Collections;
using Random = UnityEngine.Random;
using MixedReality.Toolkit;
using MixedReality.Toolkit.Input;
using UnityEngine.XR.Interaction.Toolkit;
using System.Collections.Generic;
using Microsoft.MixedReality.OpenXR;
using MixedReality.Toolkit.SpatialManipulation;
public class ObjectPlacement : MonoBehaviour
{
    // Object Prefabs
    public GameObject SphereObject;
    public GameObject CubeObject;
    public GameObject CarObject;
    public GameObject ArrowPrefab;
    public GameObject thankYouObject;

    // Camera and Arrow Settings
    private Camera mainCamera;
    private GameObject instantiatedArrow;
    private GameObject instantiatedObject;
    private Vector3 arrowPosition = new Vector3(0.0f, -1.0f, 4.0f);
    private Vector3 arrowScale = new Vector3(2.0f, 20.0f, 20.0f);

    // Object Positioning and Difficulty Parameters
    private double alphaHLoD;
    private double betaVLoD;
    private float distance;
    public float minDistance = 0.3f;
    public float maxDistance = 6.0f;
    public Vector3 distortionVector;
    public float scalingFactor = 1f;

    void Start()
    {
        mainCamera = Camera.main;
        ArrowCreate();
        DrawObjectPositions();

        if (thankYouObject != null)
        {
            thankYouObject.SetActive(false);
        }
    }

    private void ArrowCreate()
    {
        instantiatedArrow = Instantiate(ArrowPrefab, arrowPosition, Quaternion.identity);
        instantiatedArrow.transform.localScale = arrowScale;
        instantiatedArrow.SetActive(true);
    }

    public void DrawObjectPositions()
    {
        // Define specific ranges for each quadrant
        int hQuadrant = Random.Range(1, 5);
        int vQuadrant = Random.Range(1, 5);

        alphaHLoD = DetermineAngleRange(hQuadrant);
        betaVLoD = DetermineAngleRange(vQuadrant);

        float radius = Random.Range(minDistance, maxDistance);

        float x = radius * Mathf.Cos((float)alphaHLoD);
        float y = radius * Mathf.Sin((float)alphaHLoD);
        float z = radius * Mathf.Sin((float)betaVLoD);

        distance = Random.Range(minDistance, maxDistance);
        Vector3 objectPosition = mainCamera.transform.position + new Vector3(x, y, z);

        // Instantiate object
        GameObject prefabToInstantiate = SelectPrefab();
        instantiatedObject = Instantiate(prefabToInstantiate, objectPosition, Quaternion.identity);
        //AddSelectionEventListener(instantiatedObject);

        ApplyObjectTransformations();
    }


    private IEnumerator PauseAndInstantiateNewObject()
    {
        yield return new WaitForSeconds(1.5f);
        thankYouObject.SetActive(false);
        Destroy(instantiatedObject);
        Destroy(instantiatedArrow);
        ArrowCreate();
        DrawObjectPositions();
    }

    private double DetermineAngleRange(int quadrant)
    {
        // Define angle ranges for each quadrant to control difficult
        switch (quadrant)
        {
            case 1: return Random.Range(0, Mathf.PI / 4); // Easiest
            case 2: return Random.Range(Mathf.PI / 4, Mathf.PI / 2);
            case 3: return Random.Range(Mathf.PI / 2, 3 * Mathf.PI / 4);
            case 4: return Random.Range(3 * Mathf.PI / 4, Mathf.PI); // Hardest
            default: return 0;
        }
    }

    // private float DetermineDistanceByQuadrant(int hQuad, int vQuad)
    // {
    // Define distance ranges for each quadrant
    // int maxDifficultyQuad = Mathf.Max(hQuad, vQuad);
    // switch (maxDifficultyQuad)
    // {
    //     case 1: return Random.Range(minDistance, (minDistance + maxDistance) / 4);
    //     case 2: return Random.Range((minDistance + maxDistance) / 4, (minDistance + maxDistance) / 2);
    //     case 3: return Random.Range((minDistance + maxDistance) / 2, 3 * (minDistance + maxDistance) / 4);
    //     case 4: return Random.Range(3 * (minDistance + maxDistance) / 4, maxDistance);
    //    default: return minDistance;
    // }
    //}
    private GameObject SelectPrefab()
    {
        int objectType = Random.Range(0, 3);
        switch (objectType)
        {
            case 0: return SphereObject;
            case 1: return CubeObject;
            case 2: return CarObject;
            default: return null;
        }
    }

    private void ApplyObjectTransformations()
    {
        distortionVector = new Vector3(Random.Range(0.4f, 0.5f), Random.Range(0.4f, 0.5f), Random.Range(0.4f, 0.5f));
        scalingFactor = Random.Range(0.3f, 0.5f);
        Color color = new Color(Random.value, Random.value, Random.value);
        instantiatedObject.transform.localScale = distortionVector * scalingFactor;
        instantiatedObject.GetComponent<Renderer>().material.color = color;
        instantiatedArrow.transform.LookAt(instantiatedObject.transform);
        //SphereCollider collider = instantiatedObject.AddComponent<SphereCollider>();
        // collider.radius = 1.0f;
    }
  
    void Update()
    {
        if (thankYouObject != null)
        {
            // Set parent using SetParent method with worldPositionStays set to false
            thankYouObject.transform.SetParent(Camera.main.transform, false);
            thankYouObject.transform.localRotation = Quaternion.identity;
        }

        if (instantiatedArrow != null && instantiatedObject != null)
        {
            UpdateArrowPosition();
            if (instantiatedObject.GetComponent<MRTKBaseInteractable>().IsRaySelected)
            {
                DestroyAndInstantiateNewObject();
            }
            if (instantiatedObject.GetComponent<MRTKBaseInteractable>().IsGrabSelected)
            {
                DestroyAndInstantiateNewObject();
            }

        }
    }
    private void DestroyAndInstantiateNewObject()
    {
        Destroy(instantiatedObject);
        Destroy(instantiatedArrow);
        instantiatedArrow.SetActive(false);
        if (thankYouObject != null)
        {
            thankYouObject.SetActive(true);
            StartCoroutine(PauseAndInstantiateNewObject());
        } 
    }

    private void UpdateArrowPosition()
    {
        Vector3 cameraPosition = Camera.main.transform.position;
        Quaternion cameraRotation = Camera.main.transform.rotation;
        instantiatedArrow.transform.position = cameraPosition + cameraRotation * arrowPosition;
        Vector3 directionToObject = instantiatedObject.transform.position - instantiatedArrow.transform.position;
        Quaternion arrowRotation = Quaternion.LookRotation(directionToObject, Vector3.up);
        instantiatedArrow.transform.rotation = arrowRotation;
    }
}
