---
 
title: spring注解@mapper(componentmodel = "spring")的说明
date: 2022-09-19 10:42:23
tags:
- java
- spring
categories: 
- 工作日常
---

## spring中的@mapper(componentModel = “spring”)

前几天刚入职，在看公司代码的时候，发现了一个注解：
@mapper(componentModel = “spring”)

```java
public interface ScheduleSaveConvert {
    ScheduleSaveConvert INSTANCE = Mappers.getMapper(ScheduleSaveConvert.class);
    @Mappings({})
    ScheduleSaveBO convertScheduleSaveRequestVoToBo(ScheduleSaveRequestVO scheduleSaveRequestVO);

    ScheduleGetByBranchRequestBO convertScheduleGetByBranchRequestVoToBo(ScheduleGetByBranchRequestVO scheduleGetByBranchVO);
    @Mappings({})
    ScheduleGetPersonsRequestBO convertScheduleGetPersonsRequestVoToBo(ScheduleGetPersonsRequestVO scheduleGetPersonsRequestVO);

}
```

其中可以发现这个类被定义成一个interfece，并且没有实现类。在其他地方自动注入TodoItemMapper时只会出现红线提示，但是编译会通过，这让我有点不解。问完mentor才知道，是@mapper(componentModel = “spring”)起了作用。

该标签可以动态的完成DTO-DO之间的转换，这样程序员就可以免去写DO,VO,DTO各类之间转换关系的代码了。
编译之后，会在target包中生成对应的实现类：

```java
@Generated(
    value = "org.mapstruct.ap.MappingProcessor",
    date = "2022-09-19T09:39:50+0800",
    comments = "version: 1.3.0.Final"
), compiler: javac, environment: Java 1.8.0_144 (Oracle Corporation)
@Component
public class ScheduleSaveConvertImpl implements ScheduleSaveConvert {

    @Override
    public ScheduleSaveBO convertScheduleSaveRequestVoToBo(ScheduleSaveRequestVO scheduleSaveRequestVO) {
        if ( scheduleSaveRequestVO == null ) {
            return null;
        }

        ScheduleSaveBO scheduleSaveBO = new ScheduleSaveBO();

        scheduleSaveBO.setPersons( personRequestVOToPersonRequestBO( scheduleSaveRequestVO.getPersons() ) );
        scheduleSaveBO.setPublished( scheduleSaveRequestVO.getPublished() );
        scheduleSaveBO.setUnpublished( scheduleSaveRequestVO.getUnpublished() );
        scheduleSaveBO.setAutoPush( scheduleSaveRequestVO.isAutoPush() );

        return scheduleSaveBO;
    }

    @Override
    public ScheduleGetByBranchRequestBO convertScheduleGetByBranchRequestVoToBo(ScheduleGetByBranchRequestVO scheduleGetByBranchVO) {
        if ( scheduleGetByBranchVO == null ) {
            return null;
        }

        ScheduleGetByBranchRequestBO scheduleGetByBranchRequestBO = new ScheduleGetByBranchRequestBO();

        scheduleGetByBranchRequestBO.setPersonId( scheduleGetByBranchVO.getPersonId() );
        List<String> list = scheduleGetByBranchVO.getUnitId();
        if ( list != null ) {
            scheduleGetByBranchRequestBO.setUnitId( new ArrayList<String>( list ) );
        }

        return scheduleGetByBranchRequestBO;
    }

    @Override
    public ScheduleGetPersonsRequestBO convertScheduleGetPersonsRequestVoToBo(ScheduleGetPersonsRequestVO scheduleGetPersonsRequestVO) {
        if ( scheduleGetPersonsRequestVO == null ) {
            return null;
        }

        ScheduleGetPersonsRequestBO scheduleGetPersonsRequestBO = new ScheduleGetPersonsRequestBO();

        scheduleGetPersonsRequestBO.setUnitId( scheduleGetPersonsRequestVO.getUnitId() );
        scheduleGetPersonsRequestBO.setPersonId( scheduleGetPersonsRequestVO.getPersonId() );
        scheduleGetPersonsRequestBO.setStartDate( scheduleGetPersonsRequestVO.getStartDate() );
        scheduleGetPersonsRequestBO.setEndDate( scheduleGetPersonsRequestVO.getEndDate() );
        scheduleGetPersonsRequestBO.setPageNum( scheduleGetPersonsRequestVO.getPageNum() );
        scheduleGetPersonsRequestBO.setPageSize( scheduleGetPersonsRequestVO.getPageSize() );

        return scheduleGetPersonsRequestBO;
    }

    protected BreakTimeBO breakTimeVOToBreakTimeBO(BreakTimeVO breakTimeVO) {
        if ( breakTimeVO == null ) {
            return null;
        }

        BreakTimeBOBuilder breakTimeBO = BreakTimeBO.builder();

        breakTimeBO.beginTime( breakTimeVO.getBeginTime() );
        breakTimeBO.endTime( breakTimeVO.getEndTime() );
        breakTimeBO.hours( breakTimeVO.getHours() );

        return breakTimeBO.build();
    }

    protected List<BreakTimeBO> breakTimeVOListToBreakTimeBOList(List<BreakTimeVO> list) {
        if ( list == null ) {
            return null;
        }

        List<BreakTimeBO> list1 = new ArrayList<BreakTimeBO>( list.size() );
        for ( BreakTimeVO breakTimeVO : list ) {
            list1.add( breakTimeVOToBreakTimeBO( breakTimeVO ) );
        }

        return list1;
    }

    protected TaskBO taskVOToTaskBO(TaskVO taskVO) {
        if ( taskVO == null ) {
            return null;
        }

        TaskBOBuilder taskBO = TaskBO.builder();

        taskBO.scheduleDate( taskVO.getScheduleDate() );
        taskBO.personId( taskVO.getPersonId() );
        taskBO.beginTime( taskVO.getBeginTime() );
        taskBO.endTime( taskVO.getEndTime() );
        taskBO.taskId( taskVO.getTaskId() );
        taskBO.taskName( taskVO.getTaskName() );
        taskBO.color( taskVO.getColor() );
        taskBO.hours( taskVO.getHours() );

        return taskBO.build();
    }

    protected List<TaskBO> taskVOListToTaskBOList(List<TaskVO> list) {
        if ( list == null ) {
            return null;
        }

        List<TaskBO> list1 = new ArrayList<TaskBO>( list.size() );
        for ( TaskVO taskVO : list ) {
            list1.add( taskVOToTaskBO( taskVO ) );
        }

        return list1;
    }

    protected ScheduleResultModelBO scheduleResultModelVOToScheduleResultModelBO(ScheduleResultModelVO scheduleResultModelVO) {
        if ( scheduleResultModelVO == null ) {
            return null;
        }

        ScheduleResultModelBO scheduleResultModelBO = new ScheduleResultModelBO();

        scheduleResultModelBO.setScheduleDate( scheduleResultModelVO.getScheduleDate() );
        scheduleResultModelBO.setPersonId( scheduleResultModelVO.getPersonId() );
        scheduleResultModelBO.setEmployeeId( scheduleResultModelVO.getEmployeeId() );
        scheduleResultModelBO.setTrueName( scheduleResultModelVO.getTrueName() );
        scheduleResultModelBO.setTimeClassId( scheduleResultModelVO.getTimeClassId() );
        scheduleResultModelBO.setTimeClassCode( scheduleResultModelVO.getTimeClassCode() );
        scheduleResultModelBO.setBeginTime( scheduleResultModelVO.getBeginTime() );
        scheduleResultModelBO.setEndTime( scheduleResultModelVO.getEndTime() );
        scheduleResultModelBO.setBreakBeginTime( scheduleResultModelVO.getBreakBeginTime() );
        scheduleResultModelBO.setBreakEndTime( scheduleResultModelVO.getBreakEndTime() );
        scheduleResultModelBO.setHours( scheduleResultModelVO.getHours() );
        scheduleResultModelBO.setStatus( scheduleResultModelVO.getStatus() );
        scheduleResultModelBO.setLocker( scheduleResultModelVO.getLocker() );
        scheduleResultModelBO.setLocked( scheduleResultModelVO.isLocked() );
        scheduleResultModelBO.setType( scheduleResultModelVO.getType() );
        scheduleResultModelBO.setClassGroupId( scheduleResultModelVO.getClassGroupId() );
        scheduleResultModelBO.setClassGroupName( scheduleResultModelVO.getClassGroupName() );
        scheduleResultModelBO.setRemark( scheduleResultModelVO.getRemark() );
        scheduleResultModelBO.setRemarkUpdatedUser( scheduleResultModelVO.getRemarkUpdatedUser() );
        scheduleResultModelBO.setUnitId( scheduleResultModelVO.getUnitId() );
        scheduleResultModelBO.setBatchNumber( scheduleResultModelVO.getBatchNumber() );
        scheduleResultModelBO.setTaskId( scheduleResultModelVO.getTaskId() );
        scheduleResultModelBO.setTaskName( scheduleResultModelVO.getTaskName() );
        scheduleResultModelBO.setBreakTime( breakTimeVOListToBreakTimeBOList( scheduleResultModelVO.getBreakTime() ) );
        scheduleResultModelBO.setTask( taskVOListToTaskBOList( scheduleResultModelVO.getTask() ) );
        scheduleResultModelBO.setDeleted( scheduleResultModelVO.isDeleted() );
        scheduleResultModelBO.setPublishStatus( scheduleResultModelVO.getPublishStatus() );
        scheduleResultModelBO.setAttendonDate( scheduleResultModelVO.getAttendonDate() );
        scheduleResultModelBO.setDimissionDate( scheduleResultModelVO.getDimissionDate() );
        scheduleResultModelBO.setAreaId( scheduleResultModelVO.getAreaId() );
        scheduleResultModelBO.setJobId( scheduleResultModelVO.getJobId() );
        scheduleResultModelBO.setOriginalScheduleDate( scheduleResultModelVO.getOriginalScheduleDate() );
        scheduleResultModelBO.setReferDay( scheduleResultModelVO.getReferDay() );
        scheduleResultModelBO.setScheduleType( scheduleResultModelVO.getScheduleType() );

        return scheduleResultModelBO;
    }

    protected List<ScheduleResultModelBO> scheduleResultModelVOListToScheduleResultModelBOList(List<ScheduleResultModelVO> list) {
        if ( list == null ) {
            return null;
        }

        List<ScheduleResultModelBO> list1 = new ArrayList<ScheduleResultModelBO>( list.size() );
        for ( ScheduleResultModelVO scheduleResultModelVO : list ) {
            list1.add( scheduleResultModelVOToScheduleResultModelBO( scheduleResultModelVO ) );
        }

        return list1;
    }

    protected PersonRequestBO personRequestVOToPersonRequestBO(PersonRequestVO personRequestVO) {
        if ( personRequestVO == null ) {
            return null;
        }

        PersonRequestBO personRequestBO = new PersonRequestBO();

        personRequestBO.setUpdated( scheduleResultModelVOListToScheduleResultModelBOList( personRequestVO.getUpdated() ) );
        personRequestBO.setDeleted( scheduleResultModelVOListToScheduleResultModelBOList( personRequestVO.getDeleted() ) );

        return personRequestBO;
    }
}

```

