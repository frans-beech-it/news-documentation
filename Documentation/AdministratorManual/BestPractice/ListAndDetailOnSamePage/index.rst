.. ==================================================
.. FOR YOUR INFORMATION
.. --------------------------------------------------
.. -*- coding: utf-8 -*- with BOM.

.. include:: ../../../Includes.txt


Advanced Fluid integrations
---------------------------



List and detail on the same page
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* Make sure to enable both list views in configuration ts-plugin-tx-news-em-removelistactionfromflexforms_
* Add template layout option using TsConfig ::

    tx_news.templateLayouts {
      firstAsDetail = First as detail
    }

* Adjust TypoScript so controller and action aren't added to the generated links ::

    plugin.tx_news {
      mvc.callDefaultActionIfActionCantBeResolved = 1
      settings.link.skipControllerAndAction = 1
    }

* Add 2 ext:news plugins on your page:

  - Plugin 1: **Detail**
  
    - What to display: ``List view``
    - Max records displayed: ``1``
    - Hide the pagination: ``checked``
    - PageId for single news display: ``*current page*``
    - Template Layout: ``First as detail``

  - plugin 2: **List**
  
    - What to display: ``List view (without overloading detail view)``
    - PageId for single news display: ``*current page*``

* Move content from ``Templates/News/Detail.html`` to ``Partials/Detail.html`` and changes ``Templates/News/Detail.html`` to use the new created partial
  
  ``EXT:your_extension/Resources/Private/Ext/News/Templates/News/Detail.html`` ::
  
    {namespace n=GeorgRinger\News\ViewHelpers}
  
    <f:layout name="Detail.html" />
    
    <f:section name="content">
      <f:render partial="Detail" arguments="{newsItem:newsItem,settings:settings}" />
    </f:section>
  
  ``EXT:your_extension/Resources/Private/Ext/News/Partials/Detail.html`` ::  

    {namespace n=GeorgRinger\News\ViewHelpers}
    
    <f:if condition="{newsItem}">
		     ... rest of the contents of News/Detail.html
		</f:if>

* Add template layout switch to ``Templates/News/List.html``

  ``EXT:your_extension/Resources/Private/Ext/News/Templates/News/List.html`` ::
  
    {namespace n=GeorgRinger\News\ViewHelpers}
    <f:layout name="General" />
    
    <f:section name="content">
    	<f:if condition="{news}">
    		<f:then>
    			<f:if condition="{0:settings.templateLayout} == {0:'firstAsDetail'}">
    				<f:then>
    					<f:for each="{news}" as="newsItem" iteration="iterator">
    						<f:if condition="{iterator.isFirst}">
    							<f:render partial="Detail" arguments="{newsItem:newsItem,settings:settings}" />
    						</f:if>
    					</f:for>
    				</f:then>
    				<f:else>
    					<div class="news-list-view">
    						<f:if condition="{settings.hidePagination}">
    							<f:then>
    								<f:for each="{news}" as="newsItem" iteration="iterator">
    									<f:render partial="List/Item" arguments="{newsItem:newsItem,settings:settings,iterator:iterator}" />
    								</f:for>
    							</f:then>
    							<f:else>
    								<n:widget.paginate objects="{news}" as="paginatedNews" configuration="{settings.list.paginate}" initial="{offset:settings.offset,limit:settings.limit}">
    									<f:for each="{paginatedNews}" as="newsItem" iteration="iterator">
    										<f:render partial="List/Item" arguments="{newsItem:newsItem,settings:settings,iterator:iterator}" />
    									</f:for>
    								</n:widget.paginate>
    							</f:else>
    						</f:if>
    					</div>
    				</f:else>
    		</f:then>
    		<f:else>
    			<div class="no-news-found">
    				<f:translate key="list_nonewsfound" />
    			</div>
    		</f:else>
    	</f:if>
    </f:section>
