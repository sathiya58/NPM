



collections:
{
  "users": {
    "_id": "ObjectId",
    "name": "String",
    "email": "String",
    "attendance": [
      {
        "date": "Date",
        "status": "String" // 'Present' or 'Absent'
      }
    ],
    "tasks": [
      {
        "task_id": "ObjectId",
        "submitted": "Boolean",
        "submission_date": "Date"
      }
    ],
    "codekata_problems_solved": "Number",
    "mentor_id": "ObjectId"
  },
  "codekata": {
    "_id": "ObjectId",
    "problem": "String",
    "difficulty": "String" // e.g., 'Easy', 'Medium', 'Hard'
  },
  "attendance": {
    "user_id": "ObjectId",
    "date": "Date",
    "status": "String" // 'Present' or 'Absent'
  },
  "topics": {
    "_id": "ObjectId",
    "name": "String",
    "date": "Date"
  },
  "tasks": {
    "_id": "ObjectId",
    "name": "String",
    "date": "Date",
    "topic_id": "ObjectId"
  },
  "company_drives": {
    "_id": "ObjectId",
    "company_name": "String",
    "date": "Date",
    "students_appeared": [
      "ObjectId" // user_ids
    ]
  },
  "mentors": {
    "_id": "ObjectId",
    "name": "String",
    "mentees": [
      "ObjectId" // user_ids
    ]
  }
}




 1. Find all the topics and tasks which are taught in the month of October.
 db.topics.aggregate([
  {
    $match: {
      date: {
        $gte: new ISODate("2020-10-01"),
        $lt: new ISODate("2020-11-01")
      }
    }
  },
  {
    $lookup: {
      from: "tasks",
      localField: "_id",
      foreignField: "topic_id",
      as: "related_tasks"
    }
  }
]);



2. Find all the company drives which appeared between 15-Oct-2020 and 31-Oct-2020.
db.company_drives.find({
  date: {
    $gte: new ISODate("2020-10-15"),
    $lte: new ISODate("2020-10-31")
  }
});



3. Find all the company drives and students who appeared for the placement.
db.company_drives.aggregate([
  {
    $lookup: {
      from: "users",
      localField: "students_appeared",
      foreignField: "_id",
      as: "students_details"
    }
  }
]);



4. Find the number of problems solved by the user in CodeKata.
db.codekata.aggregate([
  {
    $group: {
      _id: "$user_id",
      total_problems_solved: { $sum: "$problems_solved" }
    }
  }
]);



5. Find all the mentors with mentee counts greater than 15.
db.mentors.find({
  $expr: { $gt: [{ $size: "$mentees" }, 15] }
});



6. Find the number of users who are absent and tasks are not submitted between 15-Oct-2020 and 31-Oct-2020.
db.users.aggregate([
  {
    $match: {
      "attendance": {
        $elemMatch: {
          date: { $gte: new ISODate("2020-10-15"), $lte: new ISODate("2020-10-31") },
          status: "Absent"
        }
      },
      "tasks_submitted": {
        $elemMatch: {
          submitted: false,
          date: { $gte: new ISODate("2020-10-15"), $lte: new ISODate("2020-10-31") }
        }
      }
    }
  },
  {
    $count: "absent_and_not_submitted"
  }
]);


![alt text](<Screenshot 2024-11-18 170052.png>)
![alt text](<Screenshot 2024-11-18 170106.png>)
![alt text](<Screenshot 2024-11-18 170118.png>)
![alt text](<Screenshot 2024-11-18 170139.png>)
![alt text](<Screenshot 2024-11-18 170205.png>)
![alt text](<Screenshot 2024-11-18 170205-1.png>)