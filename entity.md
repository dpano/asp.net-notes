
## Entity Framework notes

### Relationships

#### One to many

```cs
public class Student {
   public int ID { get; set; }
   public string LastName { get; set; }
   public string FirstMidName { get; set; }
   public DateTime EnrollmentDate { get; set; }
	
   public virtual ICollection<Enrollment> Enrollments { get; set; }
}

public class Enrollment {

   public int EnrollmentID { get; set; }
   public int CourseID { get; set; }
   public int StudentID { get; set; }
	
   public Grade? Grade { get; set; }
   public virtual Course Course { get; set; }
   public virtual Student Student { get; set; }
}
```
