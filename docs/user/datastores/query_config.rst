Query Properties
================

GeoMesa provides advanced query capabilities through GeoTools query hints. You can use these hints to control
various aspects of query processing, or to trigger distributed analytic processing.

.. _query_hints:

Setting Query Hints
-------------------

Query hints can be set in two ways - programmatically or through GeoServer requests.

Programmatic Hints
^^^^^^^^^^^^^^^^^^

To set a hint directly in a query:

.. code-block:: java

    import org.geotools.data.Query;

    Query query = new Query("typeName");
    query.getHints().put(key, value);

Note that query hint values must match the class type of the query hint. See below for available hints and their types.

GeoServer Hints
^^^^^^^^^^^^^^^

To set a hint through GeoServer, modify your query URL to use the ``viewparams`` request parameter:

.. code-block:: none

    ...&viewparams=key1:value1;key2:value2;

Hint values will be converted into the appropriate types. See below for available hints and their accepted values.

Loose Bounding Box
------------------

By default, GeoMesa uses a less precise filter for primary bounding box queries. This is faster, and in most cases
will not change the results returned. However, certain use-cases require an exact result. This can be set
at the data store configuration level, or overridden per query.

===================== =========== =====================
Key                   Type        GeoServer Conversion
===================== =========== =====================
QueryHints.LOOSE_BBOX ``Boolean`` ``true`` or ``false``
===================== =========== =====================


.. tabs::

    .. code-tab:: java

        import org.locationtech.geomesa.index.conf.QueryHints;

        query.getHints().put(QueryHints.LOOSE_BBOX(), Boolean.FALSE);

    .. code-tab:: scala

        import org.locationtech.geomesa.index.conf.QueryHints

        query.getHints.put(QueryHints.LOOSE_BBOX, false)

    .. tab:: GeoServer

        ``...&viewparams=LOOSE_BBOX:false``

Exact Counts
------------

By default, GeoMesa uses an estimate for counts. In certain cases, users may require exact counts. This may
be set through the system property ``geomesa.force.count`` or per query. Note, however, that exact counts
are expensive to calculate.

====================== =========== =====================
Key                    Type        GeoServer Conversion
====================== =========== =====================
QueryHints.EXACT_COUNT ``Boolean`` ``true`` or ``false``
====================== =========== =====================

.. tabs::

    .. code-tab:: java

        import org.locationtech.geomesa.index.conf.QueryHints;

        query.getHints().put(QueryHints.EXACT_COUNT(), Boolean.TRUE);

    .. code-tab:: scala

        import org.locationtech.geomesa.index.conf.QueryHints

        query.getHints.put(QueryHints.EXACT_COUNT, true)

    .. tab:: GeoServer

        ``...&viewparams=EXACT_COUNT:true``

Query Index
-----------

GeoMesa may be able to use several different indices to satisfy a particular query. For example,
a query with a spatial filter and an attribute filter could potentially use either the primary
spatial index or the attribute index. GeoMesa uses cost-based query planning to pick the best index;
however, the index can be overridden if desired.

====================== ======================= ===========================
Key                    Type                    GeoServer Conversion
====================== ======================= ===========================
QueryHints.QUERY_INDEX ``GeoMesaFeatureIndex`` index name, or name:version
====================== ======================= ===========================

.. tabs::

    .. code-tab:: java

        import org.locationtech.geomesa.accumulo.index.z2.Z2Index$;
        import org.locationtech.geomesa.index.conf.QueryHints;

        query.getHints().put(QueryHints.QUERY_INDEX(), Z2Index$.MODULE$);

    .. code-tab:: scala

        import org.locationtech.geomesa.accumulo.index.z2.Z2Index
        import org.locationtech.geomesa.index.conf.QueryHints

        query.getHints.put(QueryHints.QUERY_INDEX, Z2Index)

    .. tab:: GeoServer

        ``...&viewparams=QUERY_INDEX:z2``

Query Planning
--------------

As explained above, GeoMesa uses cost-based query planning to determine the best index for a given query.
If cost-based query planning is not working as desired, the legacy heuristic-based query
planning can be used as a fall-back. ``Stats`` uses cost-based planning; ``Index`` uses heuristic-based planning.

========================== ================== ======================
Key                        Type               GeoServer Conversion
========================== ================== ======================
QueryHints.COST_EVALUATION ``CostEvaluation`` ``stats`` or ``index``
========================== ================== ======================


.. tabs::

    .. code-tab:: java

        import org.locationtech.geomesa.index.api.QueryPlanner.CostEvaluation;
        import org.locationtech.geomesa.index.conf.QueryHints;

        query.getHints().put(QueryHints.COST_EVALUATION(), CostEvaluation.Index());

    .. code-tab:: scala

        import org.locationtech.geomesa.index.api.QueryPlanner.CostEvaluation
        import org.locationtech.geomesa.index.conf.QueryHints

        query.getHints.put(QueryHints.COST_EVALUATION, CostEvaluation.Index)

    .. tab:: GeoServer

        ``...&viewparams=COST_EVALUATION:index``
